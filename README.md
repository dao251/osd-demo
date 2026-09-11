<h3>This repository contains OpenSeadragon (OSD) <a href="https://dao251.github.io/osd-demo/">version comparison Demo</a></h3>

Being somewhat skeptical about some aspects of the OSD [openseadragon/openseadragon](https://github.com/openseadragon/openseadragon) design -
especially those introduced in recent versions,
I created my own fork, [dao251/openseadragon](https://github.com/dao251/openseadragon), in which I introduce three interrelated conceptual changes:

1) The dao251 fork uses **only 2x2 pyramids internally** (pyramids, similar to Microsoft's DZI).
I will write a [separate explanation](https://github.com/dao251/osd-demo/discussions/1) 
of the reasoning behind this choice (it is not merely a simplification of the internal code)
and how non-2x2 OSD tile sources are (or can be) supported.

2) The `TileSouce` class is no longer required to implement the `getTileURL(level,x,y)` method.  
Instead, the **primary method is `getTileImage(level,x,y)`**, which returns the tile image itself, rather than an URL.
It is invoked asynchronously and may itself be async.
The good old `getTileURL` can still be used: the default `getTileImage` implementation supports
the old scheme.  
This enables application to use non-HTTP tile sources - such as IndexedDB or other local storage -
or generate tiles on the fly in a clearer and more transparent way than the OSD documentation suggest.
This capability is especially important for the transition to using only 2x2 tile pyramids internally.
More details are available [here](https://github.com/dao251/osd-demo/discussions/2).

3) The **refactored rendering/drawing engine** now uses the **"first stich, then transform"** approach for displaying tiled images.
Traditional OSD versions transform (scale, rotate) and draw tiles individually, which produces numerous tiling artifacts -
seams, geometric inaccuracies, jitter, unstable images, and more. 
In the refactored engine, tiles are merged _before_ drawing, so the final rendering matches the original image pixel-for-pixel (like it was before tiling).
Besides significantly higher visual quality, the new engine also provides _better performance_ - both in drawing and tile loading.

The dao251 fork does not yet support some (minor) OSD options, including inter-layer blending, wrapHorisontal/Vertical,
and some of the tile-related events (tile-drawing, tile-unload, etc.). Most of these can be easily added within the new architecture.
Support for non‑2×2 pyramids requires clear, purely technical work.
A full list of currently unsupported features will be available [here](https://github.com/dao251/osd-demo/discussions/3).

The dao251 fork is **based on OSD v5**. 
This is because v6 introduced extensive core changes that I am particularly skeptical about.
Some features introduced in v6 outside the rendering engine (e.g. new TileSources, MouseTracker, and Overlay fixes)
will eventually be cherry-picked. I don't publish dao251/openseadragon updates very often, although newer features may appear in the demo.
For example, the webgl drawer, which is currently in alpha (quite slow and not fully developed).

You can compare the behavior of different OSD versions using the [**version comparison demo**](https://dao251.github.io/osd-demo/test/demo/_drawer.html?i=0&v=0&d=3). 
A description of what to pay attention to, where are the artifacts and other issues with traditional OSD
is available [here](https://github.com/dao251/osd-demo/discussions/4).
