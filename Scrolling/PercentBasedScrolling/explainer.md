# Percent-based scrolling

In Chromium-based browsers, one "tick" of the scroll wheel will always translate to a fixed value, scaled to the DPI of the monitor the window is on. For 96-DPI monitors, this will translate to be 100 pixels.

Although this works well for most cases, it causes problems on smaller scrollers. When the scroller viewport is only a few hundred pixels tall, each scroll tick will scroll the viewport by a large fraction of the visible content. This is often annoying, as it is more difficult to read or keep track of the material being scrolled.

In EdgeHTML, this issue was resolved by instead translating each scroll tick into a percentage. This percentage was then resolved against the visible bounds of the intended scroller, meaning each scroll tick always scrolled by the same fractional distance. The result is an appropriate scroll distance regardless of the size of the scroller.

As such, we would like to add support to use percent-based scrolling instead (behind a flag). We intend for this to become the default behavior in Edge. This will hopefully lead to a better user experience on many web pages.
