# Intent to Implement: Add full support for TSF 1.0 to Chromium on Windows

## Contact emails
siliu@microsoft.com, Amit.Jain@microsoft.com, Grisha.Lyukshin@microsoft.com

 ## Explainer

[TSF1.0 Explainer](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/master/TSF1/explainer.md)

## Design Doc

[Design Doc](https://github.com/siliu1/TSF1/blob/master/DesignDocument.md)

A TAG review is not requested, because the feature is specific to Windows platform and not intended to be introduced as web standards.

## Summary

We would like to improve Chromium text input on Windows by adding support for TSF1.0. This would enable text intelligence services and enable input methods, already supported on Windows to Chromium. We identified a work in progress crbug.com/657623 and we believe that the work there is aligned with our proposal. TSF1.0 is available starting with Windows 7 and should cover most current Windows releases that are still in use.

## Motivation

TSF makes it possible for apps to leverage new text intelligence services, such as autocorrection, text suggestions as you type, shapewriting that lets you swipe over keys without releasing your finger to type, IME reconversion after composition has ended, and other input modalities. Many customers have come to expect these features on mobile devices. This work will help bring similar capabilities to touch enabled Windows devices.

## Risks

### Interoperability and Compatibility
Most of other browsers on Windows already support TSF1.0 on Windows Platform. Chromium has partial TSF 1.0 support implemented behind a flag. There is no interoperability and compatibility issues for this feature to be turned on by default.

* IE: Shipped
* Edge: TSF3.0
* Chromium: Partial support behind a flag
* Firefox: Shipped
* Safari: N/A

### Ergonomics
Performance should not be affected, as all platforms use the same API for text input to Blink engine.

### Activation
In Chromium, the partial implementation of TSF 1.0 support is behind a runtime flag. This proposed work is using the same flag to turn the full implementation on. Text intelligence services can be used right after enabling the flag.

## Debuggability
The feature is not intended to be exposed to DevTools. TSF1.0 support is implemented and debugged at the browser process layer. WinDBG should support that and developers can set breakpoint in the class to debug the code.

## Will this feature be supported on all six Blink platforms (Windows, Mac, Linux, Chrome OS, Android, and Android WebView)?
No. Text Service Frameworks on Windows is specific to Windows platforms and will only be supported on Windows 7+.

## Link to entry on the feature dashboard
<TBD>

## Requesting approval to ship?
No.