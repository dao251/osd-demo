<h3>This repository contains OpenSeadragon (OSD) <a href="https://dao251.github.io/osd-demo/">version comparison Demo</a></h3>

Being somewhat skeptical about some aspects of the OSD [openseadragon/openseadragon](https://github.com/openseadragon/openseadragon) design,
especially those introduced in recent versions,
I created my own fork - [dao251/openseadragon](https://github.com/dao251/openseadragon) - in which I made three interrelated conceptual changes:

1) dao251 fork **internally** uses **only 2x2 pyramids** (like Microsoft's DZI).
I'll write a [separate explanation](https://github.com/dao251/osd-demo/discussions/1) 
of the reasons (and this isn't just a simplification of the internal code)
and how it can support non-2x2 OSD tile sources.

2) The `TileSouce` class is no longer required to implement the `getTileURL(level,x,y)` method.  
Instead, the **primary method is `getTileImage(level,x,y)`**, which returns the tile image itself, not an URL.
It is called asynchronously and, therefore, can itself be async.
(The good old `getTileURL` can continue to be used as before, as the default `getTileImage` implementation supports
both the old scheme and the option to use sync or async `getTileURL` as a bonus.)  
This allows the (user) application to use non-HTTP tile sources, such as IndexedDB or other local storage,
or generate tiles on the fly in a clearer and more transparent way than the OSD pages suggest.
The latter feature is especially important for the transition to using only 2x2 tile pyramids internally.
More details [here](https://github.com/dao251/osd-demo/discussions/2).

3) The **refactored rendering/drawing engine** now uses the **"first stich, then transform"** principle for displaying Tiled Images.
Traditional OSD versions draw (scale, rotate) tiles onto the screen individually, which causes a large number of tiling artifacts -
seams, incorrect geometry, jitter, unstable images, etc. 
Since tiles are now merged _before_ drawing, the resulting rendering matches the original image pixel-for-pixel (before tiling).
In addition to higher-quality rendering, the refactored engine provides _better performance_ (both drawing and loading).

The dao251 fork (yet) doesn't support some OSD options, specifically layer blending, wrapVertical/Horisontal,
and some tile-related events (tile-drawing, tile-unload, etc.). Most can be easily supported within the new architecture.
This is especially true for non-2x2 pyramids - they require clear and purely technical work.
A full list of currently unsupported features will be available [here](https://github.com/dao251/osd-demo/discussions/3).

The dao251 fork is **based on OSD v5**. 
This is because v6 began with massive core changes, which I'm particularly skeptical about.
Some changes introduced in v6 outside the rendering engine (e.g. new TileSources, MouseTracker, and Overlay fixes)
will eventually be cherry-picked. I don't publish dao251/openseadragon updates very often, although they may be available in this demo.
For example, the webgl drawer, which is currently in alpha (quite slow and not fully developed).

You can compare the behavior of different OSD versions using the [**version comparison demo**](https://dao251.github.io/osd-demo/test/demo/_drawer.html?i=0&v=0&d=3). 
A description of what to pay attention to, where are the artifacts and other issues with traditional OSD
is [here](https://github.com/dao251/osd-demo/discussions/4).
