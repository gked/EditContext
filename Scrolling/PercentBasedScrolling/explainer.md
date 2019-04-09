# Percent-based scrolling

In Chromium-based browsers, one "tick" of the scroll wheel will always translate to a fixed value, scaled to the DPI of the monitor the window is on. For 96-DPI monitors, this will translate to be 100 pixels.

Although this works well for most cases, it causes problems on smaller scrollers. When the scroller viewport is only a few hundred pixels tall, each scroll tick will scroll the viewport by a large fraction of the visible content. This is often annoying, as it is more difficult to read or keep track of the material being scrolled.

This issue can be fixed by instead translating each scroll tick into a percentage. This percentage could then be resolved against the visible bounds of the scroller on the screen, meaning each scroll tick will always scroll by the same fractional distance. The result is that we get appropriate scroll distances regardless of the size of the scroller.

As such, we will be adding a flag to use percent-based scrolling instead, and set it as the default in Anaheim. This will hopefully lead to a better user experience on many web pages.
