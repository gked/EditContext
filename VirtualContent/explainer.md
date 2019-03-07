# ARIA Virtual Content

Author: [Kevin Babbitt](https://github.com/kbabbitt)

Last updated: 2019-01-02

## Introduction

*Virtualized content* allows an author to efficiently represent a large body of information at once. In this practice, only a
fraction of the information is actually present in the markup - typically the portion that is currently visible, plus a small amount
preceding or following in order to allow for scrolling. As the user scrolls the web page, JavaScript code will load more content and
insert it into the markup at the appropriate location. This process is referred to as *realizing* the content.

Virtualized content presents challenges for users of assistive technologies (ATs). Generally, an AT's view of the document is limited
to what is physically present in the markup. Without some indication that further content exists, the AT cannot accurately describe and
navigate the web page. For example, some ATs allow the user to skim the headings in a document by jumping from one to the next. After
reading the last realized heading, the AT has no way of knowing that another heading might exist in virtualized content, nor does it
have a way of discovering that there is even virtualized content to begin with. The AT, and the user, thus incorrectly conclude that the
last heading in the document has been reached.

## Goals

* Enable ATs to recognize and account for virtualized content when navigating a web page.
* Protect the privacy of AT users by avoiding mechanisms that would allow for fingerprinting.

## Proposed API

A new attribute, `aria-virtualcontent`, indicates whether an element contains virtualized content. The default value is `none`.

A **virtual content container** is any element that has a non-default value for `aria-virtualcontent`.

A **virtual content edge** is an edge of a virtual content container where the webpage can realize content.

The `aria-virtualcontent` attribute can have one of several keyword values, or multiple values separated by spaces, establishing which edges of the container are virtual content edges: `block-end`, `block-start`, `inline-end`, `inline-start`.
*(Future expansion of the attribute value may provide hint(s) about the nature of the virtualized content. ATs could use this information when implementing features such as "navigate to next heading" or "next table" etc.)*

Marking an element as a virtual content container establishes a contract between the web page and ATs. Specifically:
* If the virtual content container is also a [scroll container](https://www.w3.org/TR/css-overflow-3/#scroll-container), the web page ***MUST*** begin steps to realize content no later than when the virtual content container is scrolled to a limit where a virtual content edge exists.
* If the virtual content container is not a [scroll container](https://www.w3.org/TR/css-overflow-3/#scroll-container), the web page ***MUST*** begin steps to realize content no later than when a virtual content edge enters the [scrollport](https://www.w3.org/TR/css-overflow-3/#scrollport) of its nearest ancestor [scroll container](https://www.w3.org/TR/css-overflow-3/#scroll-container).

## Example Use

### Initial Conditions
*This example assumes the default for English text: top-to-bottom block flow and left-to-right text direction.*
* The web page is in a steady state.
* An element with `aria-virtualcontent="block-end"` is partially visible in the viewport. This virtual content container's bottom edge is below the bottom edge of the viewport.
* The virtual content container contains several heading elements. An AT has its reading cursor on the last of these heading elements.
* The web page has been authored such that virtualized content will be realized when the virtual content container's bottom edge crosses into the viewport.

### Scenario
1. The user directs the AT to navigate to the next heading in the document.
2. The AT searches the web page's accessibility representation. It finds no subsequent heading.
3. The AT searches the parent chain of the last heading element (i.e. the current location of its reading cursor). It finds the virtual content container.
4. The AT reacts to the presence of the virtual content container. Typically it will search for a containing scroller, then ask the user agent to scroll such that the bottom edge of the virtual content container is in the viewport. *(Privacy note: APIs already exist on some operating systems that allow an AT to do this without fingerprinting the user.)* The AT might also choose to signal to the user that it has triggered such a scroll operation.
5. The user agent scrolls as requested by the AT and generates an onscroll event.
6. The web page reacts to the onscroll event. It requests and receives additional content. It might also announce to the user, via live
region, that additional content is being loaded. 
7. The web page inserts the new content into the DOM at the bottom of the virtual content container. It might also announce to the user, via live region, that additional content is available.
8. The user agent notifies the AT that the web page content has changed.
9. The AT reacts to the notification. It might announce the availability of new content to the user. It might also initiate a new search
of the webpage's accessibility representation for the next heading, followed by moving its reading cursor to that heading.

## Alternate solutions

### The `feed` role

[WAI ARIA 1.1](https://www.w3.org/TR/wai-aria-1.1/) defines the [`feed`](https://www.w3.org/TR/wai-aria-1.1/#feed) role, which
implements one solution for allowing ATs to interact with virtualized content, in the context of a scrollable list of articles.
However, `feed` has limitations:
* Because it is implemented as a role, it cannot coexist with other roles. This limits its applicability - for example, one might
imagine virtualizing portions of a spreadsheet whose role is best defined as `table` or `grid`.
* There is no explicit indication that virtualized content exists; in other words it does not allow the AT to distinguish between
the end of realized content and the actual end of content. This can result in the AT giving incorrect cues to the user as described
in the introduction.
