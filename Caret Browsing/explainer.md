# Native Caret Browsing Support Explainer

Disclaimer: this document is not a proposal to introduce a new set or to edit an existing set of JavaScript APIs but rather to explain a problem that we would to solve in Chromium project and propose some early thinking on it.

## Introduction
Caret Browsing is an essential Accessibility feature for navigating web content in the browser with keyboard navigation keys such as UP/DOWN/LEFT/RIGHT arrows, CTRL, SHIFT and other keys. The functionality can also unblock users that do not have a mouse, or any other technology that would enable mouse-like behavior, and therefore have limited and in some case no ability to select page contents, as well as navigate through text content in page elements.
The behavior is as if the content was set to document.designMode = ‘on’ with the difference that the user is not able to actually edit non-editable elements.

In Chromium, users can download Caret Browsing extension from Chrome’s Web Store. The are two problems with existing approach. 
First, extensions are not always available for use in some organizations due to enterprise policies and in cases when users navigate the web in “Incognito” mode or when Guest profile is used. Second, extensions may be more difficult for users to discover or that it may take too many steps to enable the desired behavior.

To elaborate on the first problem with extension-powered Caret Browsing is that some Enterprises do not allow installing extensions. A good example of such a case can be seen [here](https://bugs.chromium.org/p/chromium/issues/detail?id=611798&q=caret%20browsing&colspec=ID%20Pri%20M%20Stars%20ReleaseBlock%20Component%20Status%20Owner%20Summary%20OS%20Modified) a bug opened on Chromium by a consortium of Canadian banks where they seem restrict or block access to extensions.

Use cases with Incognito mode and Guest profile would also lack support for Caret Browsing, completely thus, making browsing experience, effectively, unusable.
Discoverability of the extension also lacks user friendliness. In order to install Caret Browsing extension today, you must navigate to the Accessibility section in the Settings page, follow the link to the Extensions Store where you can download the extension, restart the browser, and finally, use Caret Browsing. This may be perceived as bad user experience, especially to persons with disabilities. 

## Solutions
One of the possible solutions to these problems could be native support of Caret Browsing. This is how this feature is currently implemented in Edge, Firefox and Safari.
We propose to implement a way for users to enable Caret Browsing through pressing F7 and confirming on a subsequently shown dialog. Below are few examples where of how this is implemented in other browsers.
Caret Browsing in Edge:

