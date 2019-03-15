[Draft]

# Open WebDriver Automation

## Background

Both content authors and web browser developers have a shared desire to avoid breaking web sites and applications that users depend on. Both employ considerable effort in testing to ensure core user scenarios on websites continue to work.

## Problem

Content authors are primarily responsible for ensuring their content works in the browsers they choose to support--as they make changes to content, appropriate test collateral is updated to to ensure that content works.

Browser developers add features to the web platform, fix bugs, and improve interop--all of which have the possibility of introducing regressions to a content author's site. Browser developers try to reduce the risk of regression through various testing strategies, one of which includes testing "real world" sites and web applications. For a browser developer, it is challenging to test real world content because it involves "going deep", i.e., having special accounts to get past paywalls and login screens, but also understanding the unique experiences that a content author created and testing them (for example, authoring a post to share on a social network, etc.). Of course, browser developers can't scale to test the whole internet in this way, and so many regressions slip through the cracks.

With nearly every browser shipping faster (e.g., every 6 weeks), it can be quite taxing for content authors to re-test their core scenarios for every browser release. As a result they may forgo proactive testing against pre-release browsers, or even give up proactive testing on browsers all together, relying on their customers to reactively report content issues and regressions. When this happens regressions slip through the cracks.

Content authors are annoyed when browser developers unintentionally break their content. Browser developers are annoyed when they find regressions they caused through web platform changes. Result: both are annoyed, but may also suffer loss of revenue, loss of customer engagement, and loss of sentiment as customers take their browsing elsewhere.

Can't there be a way for both content authors and browser developers to work together to ease the testing burden?

Until relatively recently, there was no standardized way of testing content in a browser that mirrored how a user might interact with it. Today, the [webdriver specificiation](https://www.w3.org/TR/webdriver/) makes this a reality and many content authors are already using implementations of webdriver to lower their testing cost and more effectively test the user scenarios in their content. [citations needed].

## Hypothesis

Content authors might find value in externalizing the costs of running testing automation. If testing automation scripts could be advertised on the open web, for those with interest or resources to execute them, and assuming that there was a reliable mechanism for the content authors to get reports back on the results of the automation, they might be willing to allow the web community to help test their content. The testing of content behind paywalls or logins would need test accounts with test data--rather than real-content--to discourage the use of this mechanism by bad actors.

Browser developers would find value in the discovery and automated testing of content that advertised open testing, as it serves their self-interest to known that they are not introducing regressions in website and application core scenarios as defined by content authors themselves. Their "real world" testing on supported sites would be more robust and much more likely to catch regressions before the regressions are exposed to customers.

## Early Concepts

One rough idea is to re-use the `<link>` element to refer to a `rel=driver` URL that has the automation scripts for a given site. The URL would be open to the public, but would likely be rate-limited by the server (no more than 1 or 2 maximum connections per time interval for example, to discourage abuse). Perhaps a `rel=driver-report` would indicate where to send the pass/fail reports

Building on that idea is a more rigorous model where the automation script can only be consumed incrementally as the driver of the automation runs the tests. This might help ensure that the content author gets results for a single test before allowing the next test to be seen/run. (Like a "payment" for each test in the form or test results.)
