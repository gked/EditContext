﻿# Improving memory usage on Windows with Offer and Reclaim

## Motivation
Modern graphics applications use a large amount of texture data, and browsers often maintain multiple tabs that hold such resources. Efficient GPU memory usage is an important part of browser performance that affects user experience because of limited system resources.

Previously, there was no central GPU memory control mechanism in Chromium. Regardless of the size of the textures required, each tab had a local limit for its cache to prevent growing the global memory usage indefinitely. To improve upon this design, Chromium introduced Discardable GPU memory [[1](https://docs.google.com/document/d/1WoatXUZ7utZeKNcpOt3LXFPCc6STcmrtYqmgkZPrjds/edit)] that sets a global limit on GPU memory usage and deletes textures using LRU (Least recently used) cache eviction policy when memory pressure is detected. 

However, we still have the issue that the browser takes up a large amount of memory-- up to the global limit that Discardable GPU memory holds, even if part of the textures are not actually used (e.g. images are offscreen). Discardable GPU memory tries to delete the textures in LRU order as late as when the browser encounters memory pressure - which is based on system memory usage and not specifically GPU memory [[2](https://cs.chromium.org/chromium/src/base/memory/memory_pressure_monitor_win.cc?rcl=e49e97aedeed0567e34e310f653c1f58056b8f82&l=193)]. Under limited system resources, this still impacts the users' experience in that the applications running in the system are less responsive. 

## Proposal
To improve the memory usage during typical use and not just under memory pressure, we suggest to use Offer/Reclaim feature. This would allow the browser to lower GPU memory usage by returning the GPU memory to the OS (Offer) when it is not used, and take it back (Reclaim) when it is needed again.

## Solution
Offer/Reclaim uses DXGI's capability [[3](https://docs.microsoft.com/en-us/windows/desktop/api/dxgi1_5/nn-dxgi1_5-idxgidevice4)] to control the memory management. Offer returns the memory to the OS and Reclaim attempts to get it back. The client has to re-decode the textures only when the Reclaim fails. This happens when the OS already reallocated the memory to other applications. Offer/Reclaim has these benefits:

* Better memory usage under typical system load: We do not have to wait until the system experiences memory pressure any more, nor when the browser memory usage has reached the limit. When the heuristics detect that certain textures are not used, we send a GPU command to the GPU process to run DXGI's Offer function. This will decrease the browser's memory usage immediately. This would be visible in the Windows Task Manager.
* Eliminate DirectX CPU memory copy: When running low on GPU memory, DirectX copies textures from GPU to CPU to make room for new textures. Textures marked as offered will not be copied and instead Chromium will have the opportunity to re-decode and upload these textures if they have been discarded by the OS.
* Use OS capability to decide when to free the memory and when to take it back: In most cases, the OS is in a better position to make an optimal decision about how to schedule resources for applications running on the system. Offer/Reclaim attempts to defer to the OS to make that decision by leveraging the DXGI APIs.
	
We welcome your feedback and opinions on ways we can continue to help improve Chromium graphics on Windows in the future!

## Links
[1] https://docs.google.com/document/d/1WoatXUZ7utZeKNcpOt3LXFPCc6STcmrtYqmgkZPrjds/edit

[2] https://cs.chromium.org/chromium/src/base/memory/memory_pressure_monitor_win.cc?rcl=e49e97aedeed0567e34e310f653c1f58056b8f82&l=193

[3] https://docs.microsoft.com/en-us/windows/desktop/api/dxgi1_5/nn-dxgi1_5-idxgidevice4
