## Exploratory Data Analysis - Notes

##### Principles of Analytic Graphics
+ Principle 1 - Show Comparisons
 + evidence for a hypothesis is always relative to another competing hypothesis
 + always ask "compared to what?"
+ Principle 2 - Show causality, mechanism, explanation, systematic structure
 + what is your causal framework for thinking about a question?
+ Principle 3 - Show multivariate data
 + multivariate = more than 2 variables
 + the real world is multivariate
 + need to "espace flatland"
+ Principle 4 - Integration of evidence
 + completely integrate words, numbers, images, diagrams
 + data graphics should make use of many modes of data representation
 + don't let the tool drive the analysis
+ Principle 5 - Describe and document the evidence with appropriate labels, scales, sources, etc
 + a data graphic should tell a complete story that is credible
+ Principle 6 - Content is king
 + Analytic representations ultimately stand or fall depending on the quality, relevance, and integrity of their content

##### Exploratory graphs

Why use graphs in data analysis?
+ To understand data properties
+ To find patterns in data
+ To suggest modeling strategies
+ To "debug" analyses
+ To communicate results

Exploratory graphs cover:
+ To understand data properties
+ To find patterns in data
+ To suggest modeling strategies
+ To "debug" analyses

Characteristics of exploratory graphs:
+ They're made quickly
+ A large number are made
+ The goal is for personal understanding
+ Axes/legends are generally cleaned up later
+ Color/size are primarily used for information

Annual average PM2.5 averaged over the period 2008 through 2010
```r
pollution <- read.csv("data/avgpm25.csv", colClasses = c("numeric", "character", 
    "factor", "numeric", "numeric"))
head(pollution)
```

Simple Summaries of Data
+ One Dimension
 + five-number summary
 + boxplots
 + histograms
 + density plot
 + bar plot
+ Two Dimension
 + Multiple/Overlayed 1-D Plots (Lattice/ggplot2)
 + Scatterplots
 + Smooth scatterplots
+ > 2 Dimensions
 + Multiple/Overlayed 2-D Plots; coplots
 + Use color, size, shape to add dimension
 + Spinning plots
 + Actual 3-D plots (not that useful)

```r
# five number summary
summary(pollution$pm25)

# box plot
boxplot(pollution$pm25, col="blue")

# histogram
histogram(pollution$pm25, col="green", breaks=100)
# optionally
rug(pollution$pm25)

# overlaying features
boxplot(pollution$pm25, col="blue")
abline( h = 12)

# another example
histogram(pollution$pm25, col="green")
abline( v = 12, lwd = 2 )
abline( v = median(pollution$pm25), col = "magenta", lwd = 4 )

barplot( table(pollution$region), col = "wheat", main = "Number of counties in each region" )

# multiple boxplots
boxplot( pm25 ~ region, data = pollution, col = "red")

# multiple histograms
par(mfrow = c(2, 1), mar = c(4, 4, 2, 1))
hist(subset(pollution, region == "east")$pm25, col = "green")
hist(subset(pollution, region == "west")$pm25, col = "green")

# Scatterplot
with(pollution, plot(latitude, pm25))
abline(h = 12, lwd = 2, lty = 2)

# Scatterplot - using color
with(pollution, plot(latitude, pm25, col = region))
abline(h = 12, lwd = 2, lty = 2)

# Multiple Scatterplots
par(mfrow = c(1, 2), mar = c(5, 4, 2, 1))
with(subset(pollution, region == "west"), plot(latitude, pm25, main = "West"))
with(subset(pollution, region == "east"), plot(latitude, pm25, main = "East"))

```
***
##### Plotting systems in R
The Base Plotting system
+ "Artists Palette" model
+ Start with blank canvas and build up from t here
+ Start with plot function (or similar)
+ Use annotation functions to add/modify (`text`,`lines`,`points`,`axis`)
+ Convenient, mirrors how we think of building plots and analyzing data
+ Can't go back once plot has started (i.e. to adjust margins)
+ Need to plan in advance
+ Difficult to "translate" to others once a new plot has been created ( no graphical "language" )
+ Plot is just a series of R commands

Base Plot
```r
library(datasets)
data(cars)
with(cars, plot(speed, dist))
```

The Lattice System
+ Plots are created using single function call (`xyplot`, `bwplot`, etc )
+ Most useful for conditioning types of plots: Looking at how `y` changes with `x` across levels of `z`
+ Things like margin/spacing set automatically because entire plot is specified at once
+ Good for putting many, many plots on screen
+ Sometimes awkward to specify an entire plot in a single function call
+ annotation in plot is not necessarily intuitive
+ use of panel functions and subscripts difficult to weild and requires intense preparation
+ cannot "add" to the plot once it's been created

Lattice Plot
```r
library(lattice)
state <- data.frame(state.x77, region = state.region)
xyplot(Life.Exp ~ Income | region, data = state, layout = c(4, 1))
```
The ggplot2 System
+ Splits the difference between base and lattice in a number of ways
+ Automatically deals with spacing, text, titles but also allows you to annotate by "adding" to a plot
+ Superficial similarity to lattice but generally easier/more intuitive to use
+ Default mode makes many choices for you (but you can still customize to your hearts desire)
```r
library(ggplot2)
data(mpg)
qplot(displ, hwy, data = mpg)
```
***
##### Base Plotting System
The core plotting and graphics system in R is encapsulated in the following packages:
+ graphics: contains plotting functions for the "base" graphic systems, including `plot`, `hist`, `boxplot` and others
+ grDevices: contains all the code implementing the various graphics devies, including X11, PDF, PostScript PNG, etc

The lattice plotting system is implemented using the following packages:
+ lattice: contains code for producing Trellis graphics, which are independent of the "base" graphics system; includes functions like `xyplot`, `bwplot`, `levelplot`
+ grid: implements a different graphics system indepenedent of the "base" system; the lattice package builds on top of grid; we seldom call functions from the grid package directly

##### The process of making a plot
When making a plot one must first consider:
+ Where will the plot be made? on screen? in file?
+ How will plot be used?
 + Is the plot for viewing temporarily on the screen?
 + Will it be presented in a web browser?
 + Will it eventually end up in a paper that might be printed?
 + Are you using it in a presentation?
+ Is there a large amount of data going into the plot or is it just a few points?
+ Do you need to be able to dynamically resize the graphic?
+ What graphics system will you use: base, lattice, or ggplot2? (generally can't mix)
+ Base graphics are usually made piecemeal, each aspect handled separately through series of function calls; this is often conceptually simple and allows plotting to mirror the thought process
+ Lattice graphics are usually created in a single function call so all the params must be specified at once; specifying everythign at once allows R to automatically calculate the necessary spacings and font sizes
+ ggplot2 combines concepts from both base and lattics but uses independent implementation
We focus on the *base plotting system* to create graphics on the *screen device*

##### Base Graphics
Most commonly used and powerful for 2-D graphics
+ Two phases to create a base plot
 + Initialize a new plot
 + Annotating an existing plot
+ Calling `plot(x,y)` or `hist(x)` will launch a graphics device (if not already open) and draw a new plot on the device
+ If the arguments to `plot` are not a special class, then the *default* method for `plot` is called; this has *many* arguments, letting you set the title, x axis label, y axis label, etc
+ Base graphics system has *many* params that can be set and tweaked; these params are documented in `?par`; Wouldn't hurt to try to memorize this help page (instructor note)

Simple Base Graphics: Histogram
```r
library(datasets)
hist(airquality$Ozone)  ## Draw a new plot
```
Simple Base Graphics: Scatterplot
```r
library(datasets)
with(airquality, plot(Wind, Ozone))
```
Simple Base Graphics: Boxplot
```r
library(datasets)
airquality <- transform(airquality, Month = factor(Month))
boxplot(Ozone ~ Month, airquality, xlab = "Month", ylab = "Ozone (ppb)")
```
Important Base Graphics Params
+ `pch`: the plotting symbol (default is open circle)
+ `lty`: the line type (default is solid line) can be dotted, dashed, etc
+ `lwd`: the line width, specified as integer multiple
+ `col`: the plotting color as string, number, or hex; the `colors()` function gives a vector of colors by name
+ `xlab`: character string for x-axis label
+ 'ylab': same as above for y-axis
`par()` function is used to specify *global* params that affect all plots in an R session. These can be overridden when specified as args to specific plotting functions
+ `las`: orientation of axis labels on the plot
+ `bg`: the background color
+ `mar`: the margin size
+ `oma`: the outer margin size (default is 0 for all sides)
+ `mfrow`: number of plots per row, column (plots are filled row-wise)
+ `mfcol`: number of plots per row, column (plots are filled column-wise)

Some defaults
```r
par("col") 		# black
par("pch") 		# 1
par("lty")		# solid
par("bg") 		# transparent
par("mar") 		# 5.1 4.1 4.1 2.1
par("mfrow")	# 1 1
```
Base plotting functions
+ `plot`: make a scatterplot, or other type of plot depending on the class of object being plotted
+ `lines`: add lines to a plot, given a vector of x values and vectory of y values; this functions just connects the dots
+ `points`: add points to a plot
+ `text`: add text labels to a plot with x,y coords
+ `title`: add annotations to x, y axis labels, title, subtitle, outer margin
+ `mtext`: add arbitrary text to margins (inner or outer)
+ `axix`: add axis ticks/labels

Base Plot with Annotations
```r
# example 1
library(datasets)
with(airquality, plot(Wind, Ozone))
title(main = "Ozone and Wind in New York City")  ## Add a title

# example 2
with(airquality, plot(Wind, Ozone, main = "Ozone and Wind in New York City"))
with(subset(airquality, Month == 5), points(Wind, Ozone, col = "blue"))

# example 3
with(airquality, plot(Wind, Ozone, main = "Ozone and Wind in New York City", 
    type = "n"))
with(subset(airquality, Month == 5), points(Wind, Ozone, col = "blue"))
with(subset(airquality, Month != 5), points(Wind, Ozone, col = "red"))
legend("topright", pch = 1, col = c("blue", "red"), legend = c("May", "Other Months"))
```
Base Plot with Regression Line
```r
with(airquality, plot(Wind, Ozone, main = "Ozone and Wind in New York City", 
    pch = 20))
model <- lm(Ozone ~ Wind, airquality)
abline(model, lwd = 2)
```
Multiple Base Plots
```r
# example 1
par(mfrow = c(1, 2))
with(airquality, {
    plot(Wind, Ozone, main = "Ozone and Wind")
    plot(Solar.R, Ozone, main = "Ozone and Solar Radiation")
})

# example 2
par(mfrow = c(1, 3), mar = c(4, 4, 2, 1), oma = c(0, 0, 2, 0))
with(airquality, {
    plot(Wind, Ozone, main = "Ozone and Wind")
    plot(Solar.R, Ozone, main = "Ozone and Solar Radiation")
    plot(Temp, Ozone, main = "Ozone and Temperature")
    mtext("Ozone and Weather in New York City", outer = TRUE)
})
```
##### What is a graphics device?
+ something where you can make a plot appear
 + a window on computer ( screen device )
 + PDF file (file device)
 + PNG or JPEG (file device)
 + SVG file (file device)
+ When you make a plot in R, it must be sent to a specific graphics device
+ Most common place is to the screen device
 + on a mac the screen device is launched with `quartz()`
 + on windows `windows()`
 + on Linux/Unix `x11()`
+ When making a plot, consider how it will be used to determine what device the plot should be sent to
 + list of devices is found in `?Devices`; there are also devices created by users on CRAN
+ For quick visualizations and exploratory analysis, usually you want to use screen device
 + Functions like `plot` in base, `xyplot` in lattice, or `qplot` in ggplot2 will default to sending to screen device
 + On a given platform (Mac, Windows, Unix/Linux) there is only one screen device
+ For plots that may be printed out or incorporated into a document, usually a file device is more appropriate
 + There are many different file devices to choose from

##### How does a plot get created?
There are two basic approaches to plotting. The first is the most common:
1. Call a plotting function like `plot`,`xyplot`, or `qplot`
2. The plot appears on the screen device
3. Annotate plot if necessary
4. Enjoy
```r
library(datasets)
with(faithful, plot(eruptions, waiting)) ## Make plot appear on screen device
title(main = "Old Faithful Geyser data") ## Annotate with a title
```
The second approach to plotting is most commonly used for file devices:
1. Explicitly launch a graphics device
2. Call a plotting function to make a plot (Note: if using a file device, no plot appears on the screen )
3. Annotate plot if necessary
4. Explicitly close graphics device with `dev.off()` (This is very important!)
```r
pdf(file = "myplot.pdf") ## Open PDF device; create 'myplot.pdf' in my working directory
## Create plot and send to a file (no plot appears on screen)
with(faithful, plot(eruptions, waiting))
title(main = "Old Faithful Geyser data") ## Annotate plot; still nothing on screen
dev.off() ## Close the PDF file device
## Now you can view the file 'myplot.pdf' on your computer
```
##### Graphics File Devices
There are two basic types of file devices: *vector* and *bitmap* devices
Vector formats:
+ `pdf`: useful for line-type graphics, resizes well, usually portable, not efficient if a plot has many objects/points
+ `svg`: XML-based scalable vector graphics; supports animation and interactivity, potentially useful for web-based plots
+ `win.metafile`: Windows metafile format (only on Windows)
+ `postscript`: older format, also resizes well, usually portable, can be used to create encapsulated postscript files; Windows systems often don't have a postscript viewer
Bitmap formats:
+ `png`: bitmapped format, good for line drawings or images with solid colors, uses lossless compression (like the old GIF format), most web browsers can read this format natively, good for plotting many many many points, does not resize well.
+ `jpeg`: good for photographs or natural scenes, uses lossy compression, good for plotting many many many points, does not resize well, can be read by almost any computer and any web browser, not great for line drawings
+ `tiff`: Creates bitmap files in the TIFF format; supports lossless compression
+ `bmp`: a native Windows bitmapped format

##### Multiple Open Graphics Devices
+ It is possible to open multiple graphics devices (screen, file, or both) for example when viewing multiple plots at once
+ Plotting can only occur on one graphics device at a time
+ The *currently active* graphics device can be found by calling `dev.cur()`
+ Every open graphics device is assigned an interger >=2
+ You can change the active graphics device with `dev.set(<integer>)`

##### Copying plots
Copying a plot to another device can be useful because some plots require a lot of code and it can be a pain to type all of that in again for a different device
+ `dev.copy`: copy a plot from one device to another
+ `dev.copy2pdf`: specifically copy a plot to a PDF file
NOTE: Copying a plot is not an exact operation so the result may not be identical to the original
```r
library(datasets)
with(faithful, plot(eruptions, waiting)) ## Create plot on screen device
title(main = "Old Faithful Geyser data") ## Add a main title
dev.copy(png, file = "geyserplot.png") ## Copy my plot to a PNG file
dev.off() ## Don't forget to close the PNG device!
```

##### Course Project 1
```r
# plot1.R
dat <- read.table('household_power_consumption.txt',sep=";",nrows=-1, header=TRUE, na.strings="?")
DT <- subset(dat, as.Date(Date, "%d/%m/%Y") %in% as.Date("01/02/2007", "%d/%m/%Y"):as.Date("02/02/2007", "%d/%m/%Y") )
hist( DT$Global_active_power, col="red", xlab="Global Active Power (killowatts)", ylab="Frequency",main="Global Active Power")
dev.copy(png, file="plot1.png", height=480, width=480)
dev.off()

# plot2.R
dat <- read.table('household_power_consumption.txt',sep=";",nrows=-1, header=TRUE, na.strings="?")
DT <- subset(dat, as.Date(Date, "%d/%m/%Y") %in% as.Date("01/02/2007", "%d/%m/%Y"):as.Date("02/02/2007", "%d/%m/%Y") )
plot(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Global_active_power, type="l", xlab="", ylab="Global Active Power (killowatts)")
dev.copy(png, file="plot2.png", height=480, width=480)
dev.off()

#plot3.R
dat <- read.table('household_power_consumption.txt',sep=";",nrows=-1, header=TRUE, na.strings="?")
DT <- subset(dat, as.Date(Date, "%d/%m/%Y") %in% as.Date("01/02/2007", "%d/%m/%Y"):as.Date("02/02/2007", "%d/%m/%Y") )
plot(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Sub_metering_1, type="l", xlab="", ylab="Energy sub metering")
lines(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Sub_metering_2, type="l",col="red")
lines(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Sub_metering_3, type="l",col="blue")
legend("topright",legend=c("Sub_metering_1","Sub_metering_2","Sub_metering_3"), col=c("black","red","blue"), lwd=c(2,2))
dev.copy(png, file="plot3.png", height=480, width=480)
dev.off()

#plo4.R
dat <- read.table('household_power_consumption.txt',sep=";",nrows=-1, header=TRUE, na.strings="?")
DT <- subset(dat, as.Date(Date, "%d/%m/%Y") %in% as.Date("01/02/2007", "%d/%m/%Y"):as.Date("02/02/2007", "%d/%m/%Y") )
par(mfrow = c(2, 2)) # set up 2 x 2 plot area
# first plot is from plot2
plot(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Global_active_power, type="l", xlab="", ylab="Global Active Power")
# second plot is
plot(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Voltage, type="l", xlab="datetime", ylab="Voltage")
# third plot is from plot3
plot(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Sub_metering_1, type="l", xlab="", ylab="Energy sub metering")
lines(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Sub_metering_2, type="l",col="red")
lines(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Sub_metering_3, type="l",col="blue")
legend("topright",legend=c("Sub_metering_1","Sub_metering_2","Sub_metering_3"), col=c("black","red","blue"), lwd=c(2,2), bty="n")
# last plot is same as first, but with global reactive power
plot(strptime( paste(DT$Date, DT$Time, sep=" ", collapse=NULL ), "%d/%m/%Y %H:%M:%S" ), DT$Global_reactive_power, type="l", xlab="datetime", ylab="Global_reactive_power")
dev.copy(png, file="plot4.png", height=480, width=480)
dev.off()
```

##### Week 2

##### Lattice Plotting System

The lattice plotting system is implemented wtih these packages:
+ *lattice*: contains code for producing Trellis graphics, which are independent of the "base" graphics system; includes functions like `xypot`, `bwplot`, `levelplot`
+ *grid*: implements a different graphics system independent of the "base" system; the lattice package builds on top of gri
 + we seldom call functions from the grid package directly
+ The lattice plotting sys. doesn't have a 2-phase aspect with separate plotting and annotation like in base plotting
+ All plotting/annotation is done at once with a single function call

##### Lattice Functions
+ `xyplot`: main function for creating scatterplots
+ `bwplot`: box and whiskers plots ("boxplots")
+ `histogram`: histograms
+ `stripplot`: like a boxplot but with actual points
+ `dotplot`: plot dots on "violin strings"
+ `splom`: scatterplot matrix; like `pairs` in base plotting system
+ `levelplot`,`contourplot`: for plotting "image" data

Lattice functions generally take a formula for their first argument, such as:
```r
xyplot( y ~ x | f * g, data)
```
+ we use the formula notation here, hence the `~`
+ on the left of the `~` is the y-axis variable, on the right is the x-axis var
+ f and g are conditioning variables - they're optional
 + the * indicates an interaction between two variables
+ the second arg is the data frame or list from which the variables in the formula should be looked up
 + if no data frame or list is passed, then the parent frame is used
+ if no other args are passed, there are defaults that can be used

##### simple lattice plot
```r
library(lattice)
library(datasets)

# Ex:1 simple scatterplot
xyplot(Ozone ~ Wind, data = airquality)

# Ex: 2 Convert 'Month' to a factor variable
airquality <- transform(airquality, Month = factor(Month))
xyplot(Ozone ~ Wind | Month, data = airquality, layout = c(5, 1))
```

##### Lattice behavior
Lattice f(x)s behave differently from base graphics f(x)s in one critical way
+ base graphics fx's plot data directly to the graphics device
+ lattice graphics fxs return an object of class *trellis*
+ print methods for lattic fxs actually do the work of plotting the data on the graphics device
+ lattice fxs return *plot objects* that can, in principle, be stored but it's usually better to just save the code + data
+ on the command line, trellis objects are *auto-printed* so that it appears the fx is plotting the data
```r
p <- xyplot(Ozone ~ Wind, data = airquality)  ## Nothing happens!
print(p)  ## Plot appears

xyplot(Ozone ~ Wind, data = airquality)  ## Auto-printing
```
##### Lattice Panel Functions
+ Lattice functions have a panel function which controls what happens inside each panel of the plot.
+ The lattice package comes with default panel functions, but you can supply your own if you want to customize what happens in each panel
+ Panel functions receive the x/y coordinates of the data points in their panel (along with any optional arguments)
```r
set.seed(10)
x <- rnorm(100)
f <- rep(0:1, each = 50)
y <- x + f - f * x + rnorm(100, sd = 0.5)
f <- factor(f, labels = c("Group 1", "Group 2"))
xyplot(y ~ x | f, layout = c(2, 1))  ## Plot with 2 panels


## Custom panel function
xyplot(y ~ x | f, panel = function(x, y, ...) {
    panel.xyplot(x, y, ...)  ## First call the default panel function for 'xyplot'
    panel.abline(h = median(y), lty = 2)  ## Add a horizontal line at the median
})

## Custom panel function - regression line
xyplot(y ~ x | f, panel = function(x, y, ...) {
    panel.xyplot(x, y, ...)  ## First call default panel function
    panel.lmline(x, y, col = 2)  ## Overlay a simple linear regression line
})
```

##### Plotting with ggplot2
What is it?
+ Grammar of graphics represents an absraction of graphics ideas/objects
+ Think "verb", "noun", "adjective" for graphics
+ Allows for a "theory" of graphics on which to build new graphics and graphics objects
+ "Shorten the distance from mind to page"

Grammar of Graphics
```
In brief, the grammar tells us that a stistical graphic is a mapping from data to aesthetic attributes (colour, shape size) of geometric objects (points, lines, bars). The plot may also contain statistical transforamtion of the data and is drawn on a specific coordinate system"
```

##### The basics: `qplot()`
+ Works much like the `plot` function in base graphics system
+ Looks for data in a data frame, similar to lattice or in parent environ.
+ Plots are made up of aesthetics (size, shape color) and geoms (points, lines)
+ Factors are important for indicating subsets of the data (if they are to have different properties); they should be *labeled*
+ The qplot() hides what goes on underneath, which is ok for most ops.
+ ggplot() is the core fx and very flexible for doing things qplot() can't

Modifying aesthetics
`qplot(displ, hwy, data = mpg, color = drv)`
Adding a gemo
`qplot(displ, hwy, data = mpg, geom = c("point", "smooth"))`
Histograms
`qplot(hwy,	data = mpg, fill = drv)`
Facets
`qplot(displ, hwy, data = mpg, facets = . ~ drv)`
Histogram of eNO
`qplot(log(eno), data = maacs)`
Histogram by Group
`qplot(log(eno), data = maacs, fill = mopos)`
Density Smooth
`qplot(log(eno),	data	=	maacs,	geom	=	"density")`
`qplot(log(eno),	data	=	maacs,	geom	=	"density",	color	=	mopos)`
Scatterplots: eNO vs PM2.5
`qplot(log(pm25),	log(eno),	data	=	maacs,	color	=	mopos,	geom	=	c("point",	"smooth"),	method	=	"lm")`
##### Summary of qplot()
+ The qplot() fx is the analog to plot() but with many built in features
+ Syntax somewhere in b/w base/lattice
+ Produces very nice graphics, essentially publication ready
+ Difficult to go against the grain/customize (don't bother; use full ggplot2 power in that case)
##### Basic components of a ggplot2 plot
+ a data frame
+ aesthetic mappings: how data are mapped to color, size
+ geoms: geometric objects like points, lines, shapes
+ facets: for conditional plots
+ stats: statistical transformations like binning, quantiles, smoothing
+ scales: what scale an aesthetic map uses (ex: male = red, female = blue)
+ coordinate system

##### Building plots with ggplot2
+ when building in ggplot2 the "artists palette" model may be the closest analogy
+ plots are built up in layers
 + plot the data
 + overlay a summary
 + metadata and annotation

Build up in layers
```r
# data frame
head(maacs)

# initial call to ggplot and aesthetics
g <- ggplot(maacs, aes(logpm25, NocturnalSympt))

# summary of ggplot object
summary(g)

# no plot yet
g <- ggplot(maacs, aes(logpm25, NocturnalSympt))

# explicitly save and print ggplot object
p <-  g + geom_point()

# auto print plot object without saving
g + geom_point()

# first plot with point layer
g <- ggplot(maacs, aes(logpm25, NocturnalSympt))
g + geom_point()

# adding more layers: smooth
g + geom_point() + geom_smooth()
g + geom_point() + geom_smooth(method = "lm”)

# adding more layers: facets
g + geom_point() + facet_grid(. ~ bmicat) + geom_smooth(method = "lm")
```

##### Annotation
+ labels: xlab(), ylab(), labs(), ggtitle()
+ each of the "geom" functions has options to modify
+ for things that only make sense globally, use theme()
 + example: theme(legend.positions = "none")
+ two standard appearance themes are included
 + `theme_gray()`
 + `theme_bw()`

##### Modifying aesthetics
