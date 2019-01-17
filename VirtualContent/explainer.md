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

A new attribute, `aria-virtualcontent`, indicates whether the element is a placeholder for virtualized content.

The default value is `none`. Any value other than `none` indicates that the element is a placeholder for virtualized content.
*(Defining the attribute value to be an arbitrary string, rather than a Boolean value, leaves flexibility for the value to provide a
hint about the nature of the virtualized content. ATs could use this information when implementing features such as "navigate to next
heading" or "next table" etc.)*

The presence of a placeholder establishes a contract between the web page and ATs with the following requirements:
* The web page ***MUST*** begin steps to realize the content no later than when the placeholder is scrolled into view.
* The web page ***MUST*** insert the realized content as descendant(s) of the placeholder.
*(This requirement enables an AT to draw a connection between the placeholder and realized content when other content mutations may be
occurring.)*

Note that a web page may partially realize content by first inserting the realized portion as described, and then inserting a new
placeholder as the next sibling of the just-realized placeholder.

## Example Use

### Initial Conditions
* The web page is in a steady state.
* An AT has its reading cursor on the last heading element in the markup.
* An aria-virtualcontent placeholder is below the bottom edge of the view.
* The web page has been authored such that virtualized content will be realized when the placeholder enters the view.

### Scenario
1. The user directs the AT to navigate to the next heading in the document.
2. The AT searches the web page's accessibility representation. It finds the placeholder element.
3. The AT reacts to the presence of the placeholder element. It might move its reading cursor to that placeholder, or it might leave
the reading cursor as-is and simply announce to the user that another heading may be available.
4. The AT asks the user agent to scroll the placeholder into view.
*(Privacy note: APIs already exist on some operating systems that allow an AT to do this without fingerprinting the user.)*
5. The user agent scrolls the placeholder into view and generates an onscroll event.
6. The web page reacts to the onscroll event. It requests and receives additional content. It might also announce to the user, via live
region, that additional content is being loaded. 
7. The web page inserts the new content into the DOM as a child of the placeholder element.
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
