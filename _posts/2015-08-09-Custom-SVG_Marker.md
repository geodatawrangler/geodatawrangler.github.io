---
layout: post
title: "Make A Custom Symbol for QGIS"
date: 2015-08-09
---

This is a document describing what I had to learn to create a custom point
symbol to use in [QGIS](http://qgis.org/en/site/). This process involved some
workarounds that appear to be unique to Apple OSX 10.10.

In order to accomplish this task I needed to:

* Do some research[^research].
* Have QGIS installed on your computer.
    * I am running version 2.8.2[^1]
* Have software capable of creating an SVG file.
    * In my case I chose [InkScape](https://inkscape.org/en/) is an open
      source vector drawing program that natively creates SVG files.
    * In order to use Inkscape with OSX 10.10[^2], first install
      [XQuartz](http://xquartz.macosforge.org/landing/). I had to overcome a
      bug where InkScape will not launch[^3].
* Import or draw the design that you want to use for your custom symbol.
    * In my case I'm trying to create the
      [brand](http://www.lazym8.com/lazym8.gif) of my grandfather's ranch.
      Which so far looks very rough, but the possibilities are promising.
* Save the drawing out as a
  [SVG](https://en.wikipedia.org/wiki/Scalable_Vector_Graphics) file.
* Alter the SVG file so QGIS can control the fill color the border color and
  the border width.
    * Do more research[^4] 
    * Open the SVG file in a text editor.
    * Find the line in your saved SVG file that reads
      `style="fill:#4d4d4d;stroke:#000000;stroke-width:1;stroke-linejoin:miter;stroke-miterlimit:4;stroke-dasharray:none;stroke-opacity:1"`
    * Delete that line.
    * insert the following line in it's place.
    * `fill="param(fill) #FFF" stroke="param(outline) #000" stroke-width="param(outline-width) 1"`
* Use your new marker symbol in QGIS.
    * Open the _Layer Properties_ window for a point vector layer.
    * Change to the _Style_ tab.
    * Expand the single listed _Marker_ symbol to see the _Simple marker_
      layer.
    * Click to highlight the _Simple marker_.
    * Change the _Symbol layer type_ to _SVG marker_.
    * Look for a text box containing a full path and file name. Click the
      button to the right with three dots.
    * Browse to the SVG file just edited and _Open_ it.
    * Now the various attributes are available to adjust.
* Add custom SVGs to the QGIS interface.
    * Collect all the SVGs you want to make available into a directory.
    * Open the _Options_ window from the _Settings_ menu.
    * Select the _System_ tab.
    * Click the _Add_ button above the 
      _Path(s) to search for Scalable Vector Graphic (SVG) symbols_
      list box.
    * Browse to the directory where you have placed your custom SVGs.
    * Click the _Choose_ button.
    * Now you will see your custom symbols as choices when you go into the
    _SVG marker_ window.

__Footnotes__

[^1]: The most current OSX installer is available from
    [Kyng Chaos](http://www.kyngchaos.com/software/qgis). Be sure to install
    the packages listed under Requirements.

[^2]: Download [Inkscape for OSX](https://inkscape.org/en/download/mac-os/).

[^3]: See the
    [InkScape forum](http://www.inkscapeforum.com/viewtopic.php?f=22&t=19119)
    for a discussion of the problem. I was not able to get the solutions
    discussed to work. I found that I could launch the executable that is
    hidden inside the .app bundle. If Inkscape is installed in the
    _Applications_ directory, the application can be launched at the command
    line by typing "`/Applications/Inkscape.app/Contents/MacOS/Inkscape`". To
    simplify things for myself I added that command as an item on the X11
    _Applications_ menu. So I can start InkScape by launching the X11 app
    installed from XQuartz, opening the _Applications_ menu, and choosing the
    InkScape item I added.

[^research]: [StackExchange](http://gis.stackexchange.com/questions/32982/how-to-add-my-own-symbols-to-the-single-marker-or-svg-marker-selection-list)
    is often the first step in me learning how to do something.

[^4]: Found the basic [instructions](http://blog.sourcepole.ch/2011/06/30/svg-symbols-in-qgis-with-modifiable-colors/)
    and some more [details](http://gis.stackexchange.com/questions/45180/how-to-create-svg-symbols-that-have-modifiable-fill-color-stroke-color-and-stro)
    for Inkscape generated SVG files.
    [jgrocha](http://gis.stackexchange.com/users/7348/jgrocha) provided the
    clearest answer and the one that put it all together.