# Native Caret Browsing Support Explainer

Disclaimer: this document is not a proposal to introduce a new set or to edit an existing set of JavaScript APIs but rather to explain a problem that we would to solve in Chromium project and propose some early thinking on it.

## Introduction
Disclaimer: this explainer is not a proposal to introduce a new set or to edit an existing set of JavaScript APIs but rather to explain a problem that we would to solve in Chromium project and propose some early thinking on it.
Caret Browsing is an essential Accessibility feature for navigating web content in the browser with keyboard navigation keys such as UP/DOWN/LEFT/RIGHT arrows, CTRL, SHIFT and other keys. The functionality can also unblock users that do not have a mouse, or any other technology that would enable mouse-like behavior, and therefore have limited and in some case no ability to select page contents, as well as navigate through text content in page elements.
The behavior is as if the content was set to document.designMode = ‘on’ with the difference that the user is not able to actually edit non-editable elements.
