# Arbitrary Fragments Explained

## What’s All This About?

URIs have been a fantastic way to enable people to reference content on the Web. With the addition of anchor points within Web documents (via `name` and `id`), we people were empowered to link to specific sections of a document. Unfortunately, however, that utility is not evenly distributed as it requires action on the part of the author to include those anchor points. Numerous systems have cropped up to enable automated `id`-ing of headings, but they are seldom used. They are also limited to headings only and provide no direct access to raw text content.

Enabling users to link directly to arbitrary text content would overcome the current limitations and greatly increase the richness of a link in the context of social sharing, reporting, and legal, scientific, and scholarly writing.

## Current State of Fragments on the Web

In terms of W3C recommendations, we have a handful of extant URI fragment types:

* **Named Anchors & Identified Elements** - Use of anchor (`a`) elements with a `name` attribute (obsolete in HTML5 ([https://www.w3.org/TR/html5/obsolete.html#obsolete-but-conforming-features](https://www.w3.org/TR/html5/obsolete.html#obsolete-but-conforming-features)), but still supported for backwards compatibility) and unique `id` attribute values on flow content ([https://www.w3.org/TR/html51/dom.html#kinds-of-content-flow-content](https://www.w3.org/TR/html51/dom.html#kinds-of-content-flow-content)). Both are reference-able as part of a URI via the fragment identifier ([https://www.w3.org/DesignIssues/Fragment.html](https://www.w3.org/DesignIssues/Fragment.html)).
* **Media Fragments ([https://www.w3.org/TR/media-frags/#fragment-dimensions](https://www.w3.org/TR/media-frags/#fragment-dimensions))** - Media Fragments support complex references in terms of time (the media’s duration) and space (a viewable region of the media composition). This is accomplished via use of any (or all) of a collection of name-value components: `t` for time, `xywh` for spatial data, and `track` to specify a specific audio or video track. (An `id` component has also been proposed, but is still being discussed due to implementation challenges.) Example: `example.com/media#xywh=160,120,320,24&t=10,20&track=audio&track=video`
* **SVG ([https://www.w3.org/TR/SVG/linking.html#LinksIntoSVG](https://www.w3.org/TR/SVG/linking.html#LinksIntoSVG) )** - SVG supports referencing the `id` of a child element in order to display only that element. SVG also supports an `svgView(…)` functional keyword that enables a link to alter the SVG’s display using one or more semicolon-separated named functions: `viewBox(...)`, `preserveAspectRatio(...)`, `transform(...)`, `zoomAndPan(...)`, and `viewTarget(...)`. These fucntions correspond to parameter values for attributes on the root `svg` element. Example: `MyDrawing.svg#svgView(viewBox(0,200,1000,1000))`

## Other Approaches

This is not a new challenge and solutions have been proposed and implemented in other media. It’s worth considering their approaches and their relative applicability in this situation.

* **Plain Text Files** - A proposal for enabling arbitrary text linking in plain text documents was proposed back in 2005. It involved three modes of fragment creation: positions and ranges, characters and lines, and regular expressions. While interesting, the first two approaches require a document to have a fixed line length or unchanging structure, neither of which is common on the Web. The latter is interesting but not likely to be human readable. It could, however, be a useful next step for this feature.
* **EPUB** - The EPUB standard supports a very robust means of identifying arbitrary content within. The format is not very human readable (e.g. `epubcfi(/6/4[chap01ref]!/4[body01]/10[para05]/2/1:3[yyy])`) and is heavily dependent on document structure. Document structure within an ePub is unlikely to change very often, making this a far more robust strategy than it would be on the Web (where redesigns and dynamic content are common).
* Similar approaches have involved CSS selectors and XPath, neither of which is really tailored to the selection of arbitrary text.

## Challenges

In order to facilitate arbitrary text linking, some degree of text search is required. While a phrase or sentence contained within a single element would present few challenges from a "find and highlight" scenario, the following situations should be considered:

* Repetition of the same phrase throughout a document;
* A phrase that crosses element boundaries;
* A text phrase that appears in the content with an image in the middle of it; and
* Any combination of one or more of the above.
* Tables of contents where arbitrary text links might link to themselves as well (e.g. `<a href="#arbitrary%20text">arbitrary text</a>`)
* Sensitivity to capitalization

## Related UI Concept: In-page Search

Most User Agents support some form of searching within the current web page. These tools are familiar to users and have already addressed many of the challenges presented above. It would make a lot of sense for User Agents to tie this sort of feature directly in with their existing search implementation.

## Document or Client?

Current exploration within the IndieWeb community (which refers to these as "fragmentations" - [https://indieweb.org/fragmentations](https://indieweb.org/fragmentations) ) and elsewhere have demonstrated what is possible with client-side implementations of arbitrary text linking using JavaScript. One could argue that this sort of functionality should exist at the discretion of each site owner. There counterargument is that universal applicability of this functionality would benefit users and seems in alignment with the Priority of Constituencies ([https://www.w3.org/TR/html-design-principles/#priority-of-constituencies](https://www.w3.org/TR/html-design-principles/#priority-of-constituencies)).

It’s worth pointing out that there are pros and cons to both approaches:

|  | Pros | Cons |

|-|---------|---------|

| Document | Author controls the experience | Author must define the experience for users to benefit |

| Client | * Users enjoy a shared experience across the Web * Every site gets the upgraded experience * Browsers already offer a robust in-page text search | Authors may not be able to control the experience |

## API Explorations

Kevin Marks and others within the IndieWeb community have explored a variety of approaches for denoting arbitrary text searches in a URI.

### #arbitrary%20text%20search

The IndieWeb is in favor of re-purposing the existing fragment identifier (a single hash character) with general agreement that spaces (which will need to be encoded) will provide enough differentiation from existing fragment identifiers. To use this approach, any raw hash (#) or whitespace. It’s worth noting that this approach could lead to some potential confusion for the User Agent when dealing with a link that references both an existing anchor _and_ arbitrary text.

### ##arbitrary%20text%20search

An earlier proposal from the IndieWeb community involved using a double hash prefix. The URL spec ([https://tools.ietf.org/html/rfc3986#appendix-A](https://tools.ietf.org/html/rfc3986#appendix-A)) does not allow for hash characters in a fragment, so these links will fail strict validation. HTML5 also allows for the hash character to be used in an id attribute, leading to some potential confusion for the User Agent when dealing with a link that references both an existing anchor _and_ arbitrary text.

### #search=arbitrary%20text%20search

This proposal takes a page from the direction Media Fragments have gone via a name-value component in the fragment. This would enable User Agent’s to disambiguate anchor references from arbitrary text searches.

Note: The named keyword could be anything not currently reserved as part of another specification. It would be recommended to stick to something that is human readable and brief or an abbreviation of a human readable (and familiar) term. Other options include "s" (a common shorthand for "search") "query", and "q". The question mark (?) could also be used as it is allowed in URIs but should not cause collisions with arguments passed on the GET string as it would exist after the fragment signifying hash (#).

### Additional considerations

There has been some discussion over whether white space should be represented as an encoded space (%20) or a plus (+) in the arbitrary fragment.** The latter would require** additional encoding of any literal plus characters in the arbitrary text.

## Related Ideas & Implementations

* Chrome Extention - [https://chrome.google.com/webstore/detail/fragmentions/pgajkeekgcmgglngchhmcmnkffnhihck](https://chrome.google.com/webstore/detail/fragmentions/pgajkeekgcmgglngchhmcmnkffnhihck)
* JS implementation - [https://github.com/chapmanu/fragmentions](https://github.com/chapmanu/fragmentions)
* WordPress plugins: [https://christiaanconover.com/code/wp-fragmention](https://christiaanconover.com/code/wp-fragmention) & [https://github.com/goblindegook/Fragmentions/](https://github.com/goblindegook/Fragmentions/)
* Drupal Plugin: [https://drupal.org/sandbox/ocean/2247743](https://drupal.org/sandbox/ocean/2247743)
* NY Times: [http://open.blogs.nytimes.com/2011/01/11/emphasis-update-and-source/](http://open.blogs.nytimes.com/2011/01/11/emphasis-update-and-source/)
* Save Publishing: [http://www.savepublishing.com/](http://www.savepublishing.com/)
* Business Insider: [http://www.businessinsider.com/the-inside-story-of-how-rap-genius-fired-a-cofounder--and-just-raised-40-million-annotated-2014-7#annotations/3411037](http://www.businessinsider.com/the-inside-story-of-how-rap-genius-fired-a-cofounder--and-just-raised-40-million-annotated-2014-7#annotations/3411037)
* The Guardian: [https://twitter.com/wblau/status/529713139961827329](https://twitter.com/wblau/status/529713139961827329)
* Addressable: [https://github.com/mhausenblas/addrable](https://github.com/mhausenblas/addrable) 

## Related Specifications & Concepts

* Media Fragments - [https://www.w3.org/TR/media-frags/](https://www.w3.org/TR/media-frags/) 
* CSS Selectors (only to elements though) - [https://developer.mozilla.org/docs/Web/CSS/CSS_Selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors) 
* XPath (only to elements though) - [https://developer.mozilla.org/docs/Web/XPath](https://developer.mozilla.org/en-US/docs/Web/XPath) 
* DOM Range - [https://developer.mozilla.org/docs/Web/API/Range](https://developer.mozilla.org/en/docs/Web/API/Range) 
* EPUB Canonical Fragment Identifiers - [http://www.idpf.org/epub/linking/cfi/epub-cfi.html](http://www.idpf.org/epub/linking/cfi/epub-cfi.html) 
* SVG IRI fragments - [https://www.w3.org/TR/SVG/linking.html#LinksIntoSVG](https://www.w3.org/TR/SVG/linking.html#LinksIntoSVG) 
* Web Annotations Selectors - [https://www.w3.org/TR/2017/NOTE-selectors-states-20170223/#frags](https://www.w3.org/TR/2017/NOTE-selectors-states-20170223/#frags)
* Fragment identifiers for plain text files [PDF] - [http://dret.net/netdret/docs/wilde-ht2005-textfrag.pdf](http://dret.net/netdret/docs/wilde-ht2005-textfrag.pdf) 
* Using URLs to pass parameters to web applications, widgets and gadgets - [http://internet-apps.blogspot.in/2007/11/using-urls-to-pass-parameters-to-web.html](http://internet-apps.blogspot.in/2007/11/using-urls-to-pass-parameters-to-web.html) 

## Use Cases

* social share
* research
* annotations
* marginalia
* comments
* editing
* pen interaction/highlighting

## Open Questions/Challenges

* CSS hooks?
* text containing a #
* text spanning multiple elements and/or with other elements in between (e.g., an inline `img`)
* multiple instances of matched text
* Changing structure vs. changing content
* generated content