# <code>ImageResource</code> Color Scheme Explainer

Authors: [Aaron Gustafson](https://github.com/aarongustafson)

## Introduction

With operating systems beginning to offer multiple color schemes (e.g., “dark mode” and “light mode”) and with [CSS offering a media query for that user preference](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme), it is important to enable web apps to offer appropriate iconography for the context. As such, we need to upgrade the [<code>ImageResource</code> dictionary](https://w3c.github.io/manifest/#dom-imageresource) to include an optional member indicating which color scheme a given image should be associated with.

## Goals

* Enable authors to (optionally) identify <code>ImageResource</code> (icons, etc.) within their Web App Manifest as being appropriate for specific color schemes
* Enable user agents and operating systems to display the appropriate <code>ImageResource</code>, given the user’s chosen color scheme

## Use Cases

Many websites are beginning to look to the operating system for queues about how the user would prefer their apps be rendered (e.g., “dark mode”). There is a media query which allows them to toggle the color-related styles applied to their website (or PWA), but their app icon (referenced in the Web App Manifest) remains unaffected. In certain cases, it may make sense for an app’s icon to adjust to match the user’s chosen color scheme for maximum legibility.

## API Proposal

We are proposing that a new member be added to the [<code>ImageResource</code> dictionary](https://w3c.github.io/manifest/#dom-imageresource) defined in the [Web App Manifest](https://w3c.github.io/manifest/): `color_scheme`. This optional member would accept any values allowable in [the `prefers-color-scheme` media query](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme):

```json
"icons": [
  {
    "src": "/icons/play-later.svg",
    "type": "image/svg+xml",
    "purpose": "any",
    "color_scheme": "no-preference"
  },
  {
    "src": "/icons/play-later-reverse.svg",
    "type": "image/svg+xml",
    "purpose": "any",
    "color_scheme": "dark"
  }
]
```

Without a defined `color_scheme`, the value of "no-preference" would be assigned.

## Open Questions

1. Would it make sense to use the existing [`purpose` member of an <code>ImageResource</code>](https://w3c.github.io/manifest/#dom-imageresource-purpose)?