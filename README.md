<p align="center"><img src="https://github.com/wholder/GerberPlot/blob/master/images/GerberPlot%20Screenshot.png"></p>

# GerberPlot
This code began as rewrite of the "Plotter" portion of Philipp Knirsch's "_Gerber RS-274D/X file viewer in Java_" (see: http://www.wizards.de/phil/java/rs274x.html) but, the current code has been extensively rewritten and expanded to use Java's Shape-based graphics and fix numerous small issues I found with the original code.  I've also  added more features from version 2017.05 of the Gerber Format Specification by Ucamco, such as partial support for "`AD`"-type Macros and implemented many of the Aperture primitives needed for this.  Note: some of the Aperture primitives remain untested, as I've yet to find a Gerber design that uses them. The untested code is noted in the source for the `flashAperture()` method.

Internally, the code first converts the Gerber file into a list of Shape objects in "`drawItems`".  Then, if renderMode is set to DRAW_IMAGE (the default when started), it draws this list of shapes directly to the screen.  However, if renderMode is set to `RENDER_FILLED` or `RENDER_OUTLINE`, `getBoardArea()` is called to compute an `Area` shape by using the 2D constructive geometry operations `add()` and `subtract()`.  This converts all the individual shapes in `drawItems` into a single Shape the contains all the geometry of the PCB design.  Unfortunately, computing this Area becomes exponentially inefficient with larger Gerber files because, as each new shape is added, or subtracted its takes increasinglt more time to calculate all the intersections.  So, a progress bar is displayed to show the progress of the calculation.  Once the `Area` is computed, however, it can be resized and redrawn much more quickly than when `renderMode == DRAW_IMAGE`.
### Requirements
Java 8 JDK, or later must be installed in order to compile the code.  There is also a [**Runnable JAR file**](https://github.com/wholder/GerberPlot/tree/master/out/artifacts/GerberPlot_jar) included in the checked in code that you can download.   Just double click the GerberPlot.jar file and it should start and display a window like the one shown near the top of this page.  Note: you may have to select the GerberPlot.jar file, then do a right click and select "Open" the first time you run the file to satisfy Mac OS' security checks.
### Limitations
 I've tried to adhere as close as possible to the latest revision of the Ucamco spec, version, 2017.05, which means my code may not parse some older Gerber files, such as ones that reply on on undocumented behavior, or deprecated operations.  In particular, `G36`/`G37` regions specified like this:

    G36*G01X-0001Y-0001D02*X1076D01*Y0226*X-0001*Y-0001*G37*

  Will not render because all of the coordinate values need to be followed by a D01 operation to render, such as:

    G36*G01X-0001Y-0001D02*X1076D01*Y0226D01*X-0001D01*Y-0001D01*G37*