# Native Caret Browsing Support
## Introduction
Caret Browsing enables users to navigate through web content in the browser using keyboard navigation keys, such as arrow keys for character level navigation and CTRL + arrow keys for word level navigation. Caret browsing can unblock users who do not have a mouse or other pointer device, such that they have the full range of text navigation and selection functionality within web content. With caret browsing, users can explore text as various fidelities, such as line by line or character by character.
Below is the gif showing the caret browsing in action:
![](CB_Example.gif)

## Use Cases
In Chromium, users can download caret browsing [extension](https://chrome.google.com/webstore/detail/caret-browsing/fklpgenihifpccgiifchnihilipmbffg) from Chrome’s Web Store. The are two problems with this approach. 
First, extensions are not always available for use in some organizations due to enterprise policies, in cases when users navigate the web in “Incognito” mode, or when Guest profile is used. Second, extensions may be more difficult to be discovered or that it may take too many steps to enable the desired behavior. This is especially true for people with limited abilities or those who are not accustomed to be using keyboard in the absence of a mouse.
To elaborate on the first problem with extension-powered caret browsing is that some Enterprises do not allow installing extensions. A good example of such a case can be seen [here](https://crbug.com/611798). The bug opened on Chromium by a consortium of Canadian banks where they seem restrict or block access to extensions.
Use cases with Incognito mode and Guest profile would also lack support for caret browsing completely, thus, making browsing experience effectively unusable.
Discoverability of the extension also lacks user friendliness. In order to install caret browsing extension today, you must navigate to the Accessibility section in the Settings page, follow the link to the Extensions Store where you can download the extension, restart the browser, and finally, use caret browsing. This may be perceived as bad user experience, especially by persons with different abilities.
## Goals
The goal for this proposal is to allow Chromium-based browsers users to enable caret browsing mode without needing to install extensions.
## Non-Goals
We are not proposing to replace extensions approach altogether. Existing extension users will still be able to use it.
## Solutions
One of the possible solutions to this problem could be native support of Caret Browsing. This is how this feature is currently implemented in Edge, Firefox and Internet Explorer.
We propose to implement a way for users to enable caret browsing through pressing F7 and confirming on a subsequently shown dialog. Below are a few examples of how this is implemented in other browsers.
Caret Browsing in Edge:

![](edgeCaretBrosingPrompt.png)

Caret Browsing in Firefox:

![](firefoxCaretBrosingPrompt.png)
In some cases, function keys may not be available and so alternative shortcuts would be needed.
For example, keyboard layout on Mac is inconsistent. Some keyboards have an F7 key, some have FN + F7 keys, and some have the function key space customized. While ideally we would want to keep the user experience consistent across platforms, it is not always easy to do. The alternative shortcut for Mac could be a new shortcut combination: CMD + Option + 7. 
