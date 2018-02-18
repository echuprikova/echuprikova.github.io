---
layout: post
title: Tutorial on R Shiny
summary: R, Shiny and Leaflet for Interactive Mapping
featured-img: emile-perron-190221

---


## Introduction to R


R is an integrated suite of software for data manipulation, calculation and graphical
display. Among other things it has

*  an effective data handling and storage facility,
*  a suite of operators for calculations on arrays, in particular, matrices,
*  a large, coherent, integrated collection of intermediate tools for data analysis,
*  graphical facilities for data analysis and display either directly at the computer or on hardcopy,
and
*  a well developed, simple and effective programming language (called 'S') which includes
conditionals, loops, user defined recursive functions and input and output facilities. (Indeed
most of the system supplied functions are themselves written in the S language.)

R is very much a vehicle for newly developing methods of interactive data analysis. It has
developed rapidly and has been extended by a large collection of packages. However, most
programs written in R are essentially ephemeral, written for a single piece of data analysis.

See more here https://cran.r-project.org/doc/manuals/r-release/R-intro.pdf

## Introduction to R Shiny

R Shiny is an open package from RStudio, used to build interactive web pages with R. It provides a very powerful way to share your analysis in an interactive manner with the community. The best part about R Shiny is that you do not need any knowledge of HTML, CSS or JavaScript to get started. The ease of working with Shiny has what popularized it among R users. These web applications seamlessly display R objects (like maps, plots, tables etc.) and can also be made live to allow access to anyone.

Shiny provides an automatic reactive binding between inputs and outputs which we will be discussing in the later parts of this tutorial. It also provides extensive pre-built widgets which make it possible to build elegant and powerful applications with minimal effort.


## Assignment

```{r echo=FALSE, out.width='90%'}
knitr::include_graphics('./fig/0create_project.jpg')
```

Create an interactive mapping application that facilitates visualization of population data in Munich. The application should include a map panel, where the data visualized as a thematic map and powered by a panel where the theme could be changed. The application should include a panel with the histogram showing the distribution of the phenomena. The legend should be interactively updated along with the thematic layer. The interaction with the map should provide a user with the information of the interest by placing the mouse over a region. The selected region should be highlighted with a hover.


## Get started

To get started launch RStudio.

1. Create a new project in R Studio

```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/1create_project.jpg')
```

2. Select project type as Shiny Web Application

```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/2create_project.jpg')
```

3. Define the name directory and the application location.

```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/3create_project.jpg')
```

It creates two scripts in R Studio named ui.R and server.R.

+ In new Shiny application Server and User Side are located in the same file and defined as ui.

* __UI.R__: This file creates the user interface in a shiny application. It provides interactivity to the shiny app by taking the input from the user and dynamically displaying the generated output on the screen.
* __Server.R__: This file contains the series of steps to convert the input given by user into the desired output to be displayed.

By default you will see such code:


```{r echo=FALSE, out.width='100%'}
knitr::include_graphics('./fig/1test_example.jpg')
```

4. The next step is to immediately install some packages that we need for the further work.

To install necessary R packages, run this command at your `R prompt (Console)`!. You might be asked if you want to update those packages that are already installed - "Yes":
```{r eval=FALSE}
 install.packages("shiny")
 install.packages("rgdal")
 install.packages("sp")
```


5. Besides, it is required to install package Leaflet.

```{r eval=FALSE}
 install.packages("leaflet")
```


6. When it is done, we can launch the App to see how the given example looks like.

If you launch the application you will see the default example.

```{r echo=FALSE, out.width='100%'}
knitr::include_graphics('./fig/4create_project.jpg')
```

In this application, a user might investigate Old Faithful Geyser Data, which shows waiting time between eruptions and the duration of the eruption of the Old Faithful geyser in Yellowstone National Park, Wyoming, USA. In this App, you can observe a histogram graph and interactive slider. Whenever you change the value in the slider the histogram would reactively change its appearance.


## Spatial Data Visualization in R Shiny.

#### Libraries
To call the packages we need to request the libraries in both files ui.R and server.R

```{r eval=FALSE}
library(shiny)
# mapping library
library(leaflet)
# Both 'GDAL' raster and 'OGR' vector map data can be imported into R, and 'GDAL' raster data and 'OGR' vector data exported.
library(rgdal)
# Classes and Methods for Spatial Data.
library(sp)
```

#### User  Interface (UI) design
R shiny applications adopt different UI structures. Here we will use a sidebar layout as it is a useful starting point for most applications. This layout provides a sidebar for inputs and a large main area for output. Find out more about layouts: https://shiny.rstudio.com/articles/layout-guide.html

To create a sidebar layout we need to call fluidPage. A fluid page layout consists of rows which in turn include columns. Rows exist for the purpose of making sure their elements appear on the same line (if the browser has adequate width). Columns exist for the purpose of defining how much horizontal space within a 12-unit wide grid its elements should occupy. Fluid pages scale their components in real-time to fill all available browser width. The fluid page layout might include instances of fluidRow and column within it or include the elements of the Sidebar Layout - sidebarPanel and mainPanel.

Besides, we can assign the title to our application. Let's call it "Munich in Figures".

Therefore, the application should include the following code in __ui.R__ Please copy the code.

* __ui.R__

```{r eval=FALSE}
library(shiny)
library(leaflet)
library(rgdal)
library(sp)

shinyUI(fluidPage(

  # Assign a title to your application
  titlePanel("Munich in figures"),
  # Sidebar Layout
  sidebarLayout(
  # Sidebar Panel where we will place the themes for our choropleth maps and histograms  
    sidebarPanel(
    ),
  # Main Panel should include Digital map based on the Leaflet.js, legend panel, map layers, and an overview map.
    mainPanel(
    )
  )
))

```
* __server.R__
```{r eval=FALSE}
library(shiny)
library(leaflet)
library(rgdal)
library(sp)

shinyServer(function(input, output) {

})

```


#### Data

For this assignment we are going to use Munich districts data that represent information about population density, number of inhabitants, number of foreigners and amount of households in the city of Munich according to the district division.
The data may be found in Moodle. The downloaded ZIP should be unpacked inside the Shiny project in a new folder called "shp".

The data is given in ESRI Shape format and it can be directly loaded into R memory by defining the path to the data via the ReadOGR function.  In case of working with large data, it should be stored in the database. The data should be loaded within Server Side only, and only rendered in UI through Leaflet.

```{r eval=FALSE}

districts <- readOGR("shp/Munich_Districts.shp",
                     layer = "Munich_Districts")
```
Therefore, districts should be announced in server.R as global variable __before__ the main function.
```{r eval=FALSE}
shinyServer(function(input, output)
```

The ESRI Shapefile includes among others such columns as "Pop_Densit", "Inhabitant", "Foreigners" and "Households". These attributes might be used for the choropleth mapping and statistics about the distribution of each variable.

Here we define the classes that we will use for thematic mapping based on the attribute table from the Munich_districts data.

```{r eval=FALSE}
vars <- c(
  "Population density" = "Pop_Densit",
  "Inhabitants" = "Inhabitant",
  "Foreigners" = "Foreigners",
  "Households" = "Households"
)
```

"vars"" are needed in both __server.R__ and __ui.R__.

Therefore:


* __ui.R__
```{r eval=FALSE}
library(shiny)
library(leaflet)
library(rgdal)
library(sp)
# Define the topics for the thematic maps and histogram according to the given SHP file attributes.		
vars <- c(
  "Population density" = "Pop_Densit",
  "Inhabitants" = "Inhabitant",
  "Foreigners" = "Foreigners",
  "Households" = "Households"
)
shinyUI(fluidPage(
 ........
))
```


* __server.R__
```{r eval=FALSE}
library(shiny)
library(leaflet)
library(rgdal)
library(sp)

# Define the data
districts <- readOGR("shp/Munich_Districts.shp",
                     layer = "Munich_Districts")

# Define the topics for the thematic maps and histogram according to the given SHP file attributes.				 
vars <- c(
  "Population density" = "Pop_Densit",
  "Inhabitants" = "Inhabitant",
  "Foreigners" = "Foreigners",
  "Households" = "Households"
)
shinyServer(function(input, output) {
........
})
```


## Mapping in R with Leaflet.js
The leaflet is one of the most popular open-source JavaScript libraries for interactive maps. It is commonly used by websites ranging from The New York Times and The Washington Post to GitHub and Flickr, as well as GIS specialists like OpenStreetMap, Mapbox, and CartoDB.

##### Features
* Interactive panning/zooming
* Compose maps using arbitrary combinations of: Map tiles, Markers, Polygons, Lines, Popups, GeoJSON
* Create maps right from the R console or RStudio
* Embed maps in knitr/R Markdown documents and Shiny apps
* Easily render spatial objects from the sp or sf packages, or data frames with latitude/longitude columns
* Use map bounds and mouse events to drive Shiny logic
* Display maps in non-spherical Mercator projections
* Augment map features using chosen plugins from leaflet plugins repository.


As our next step, we introduce a map in the application. To begin with, we can use tiles provided by Open Street Map and rendered via Leaflet.js.

Most Shiny output widgets are incorporated into an app by including an output (e.g. plotOutput) for the widget in the UI definition and using a render function (e.g. renderPlot) in the server function. Leaflet maps are no different; in the UI you call leafletOutput, and on the server side you assign a renderLeaflet call to the output. Inside the renderLeaflet expression, you return a Leaflet map object.

Use leafletOutput() to create a UI element, and renderLeaflet() to render the map widget.

For example:

* in __ui.R__
```{r eval=FALSE}
......
    mainPanel(
      leafletOutput("map", width = "100%", height = 600)
      )
......    
```
Where __outputId__ (output variable to read from) is equal to "map". Using unique outputId we can connect the UI and server side.
Width, height are the width and height of the map.

* in __server.R__

We define the output with outputId = "map" to __renderLeaflet()__, where a Leaflet map widget is created based on the data object "districts" (our ESRI Shape that was loaded previously). The function __leaflet()__ returns a Leaflet map widget, which stores a list of objects that can be modified or updated later. Most functions in this package have an argument map as their first argument, which makes it easy to use the __pipe operator %>%__.


```{r eval=FALSE}
......
shinyServer(function(input, output) {
    output$map <- renderLeaflet({
    leaflet(districts)%>%
    addTiles()
  })
})
```

Result:

```{r echo=FALSE, out.width='100%'}
knitr::include_graphics('./fig/5app.jpg')
```

### The Map Widget
At this point, we can concentrate on the server side and work with the file __server.R__.

#### Map Methods
You can manipulate the attributes of the map widget using a series of methods.

* __addTiles()__ adds a tile layer to the map;
* __addProviderTiles()__ adds a tile layer from a known map provider;
* __setView()__ sets the centre of the map view and the zoom level;
* __addMiniMap()__ adds a minimap to the Map. The tile provider might be chosen freely;
* __addLayersControl()__ adds UI controls to switch layers on and off;


```{r eval=FALSE}
.......
shinyServer(function(input, output) {
  output$map <- renderLeaflet({
    leaflet(districts) %>%
      # Base groups.
      # The tiles and provider tiles might be added to a group. Human-friendly group names are permitted-
      # they need not be short, identifier-style names.
      addTiles(group = "OpenStreetMap") %>%
      addProviderTiles("CartoDB.Positron", group = "CartoDB") %>%
      addProviderTiles("Esri.WorldImagery", group = "ESRI") %>%
      # Point of map view. Here we can set the coordinates and zoom level
      setView(lng = 11.557137, lat = 48.157831, zoom = 12)%>%
      # Overview map
      # toggleDisplay sets whether the minimap should have a button to minimise it. Defaults to false.
      addMiniMap(tiles = providers$CartoDB.Positron,
                 toggleDisplay = TRUE)%>%  
      # Layers
      addLayersControl(
        baseGroups = c("OpenStreetMap", "CartoDB", "ESRI")
      )

  })
})
.......
```
Please update the application to see how it looks.


Furthermore, we can add our district polygons on the map.

```{r eval=FALSE}
      %>% # pipe operator
      addPolygons(color = "white", weight = 1, smoothFactor = 0.5,
                  opacity = 1.0, fillOpacity = 0.5,
                  dashArray = "3",
                  highlightOptions = highlightOptions(color = "grey", weight = 2,
                                                      bringToFront = TRUE)
      )

```

When we added the polygons they are visualized in white color with fill opacity 0.5, and white dash border. The Smooth Factor defines how much to simplify the polyline on each zoom level (more means better performance and less accurate representation). The highlight options define how the polygons are highlighted when a mouse points on a district. In our case, we change the border from white to grey. The argument __bringToFront = TRUE__ defines whether the shape should be brought to the front on hover.

#### Colors
An important part of spatial visualization is mapping variables to colors. To define how to map values to colors we will use the in-built function for dealing with continuous input: `colorNumeric`. Though there are also other options which include `colorBin`, and `colorQuantile`; and one for categorical input, `colorFactor`.

##### Common parameters
The four color functions all have two required arguments, `palette` and `domain`.
The palette argument specifies the colors to map the data to. This argument can take one of several forms:

* The name of a preset palette from the RColorBrewer package, e.g. "`RdYlBu`", "`Accent`", or  "`Greens`".
* The full name of a viridis palette: "viridis", "magma", "inferno", or "plasma".
* A character vector of RGB or named colors, e.g. `palette()`,  c(`"#000000", "#0000FF", "#FFFFFF`"), `topo.colors(10)`.
* A function that receives a single value between 0 and 1 and returns a color, e.g.:  `colorRamp(c("#000000", "#FFFFFF"), interpolate="spline"`)

The `domain` argument tells the color function the range of input values. You can pass `NULL` here to create a palette function that doesn't have a preset range; the range will be inferred from the data each time you invoke the palette function. If you use a palette function multiple times across different data, it's important to provide a non-NULL value for the domain so the scaling between data and colors is consistent.

For example,

```{r eval=FALSE}
colorNumeric(palette, domain, na.color = "#808080", alpha = FALSE,
  reverse = FALSE)
colorBin(palette, domain, bins = 7, pretty = TRUE, na.color = "#808080",
  alpha = FALSE, reverse = FALSE)
colorQuantile(palette, domain, n = 4, probs = seq(0, 1, length.out = n + 1),
  na.color = "#808080", alpha = FALSE, reverse = FALSE)
colorFactor(palette, domain, levels = NULL, ordered = FALSE,
  na.color = "#808080", alpha = FALSE, reverse = FALSE)
```

Thus, to define the color palette we provide a sequential palette that is suited to ordered data that progress from low to high. Lightness steps
dominate the look of these schemes, with light colors for low data values to dark colors for high data values. Our palette is defined as `"YlOrRd"` which means Yellow, Orange, Red.

```{r eval=FALSE}
pal <- colorNumeric("YlOrRd", domain = districts$colorData, n = 6)
```
Here the argument `Domain` is responsible for the mapping of all the possible values. This part is necessary for the late steps when we define the mapping according to every given district attribute.

Besides we should add additional option to the method AddPolygons `fillColor = ~pal(Pop_Densit)`.

```{r eval=FALSE}
      %>% # pipe operator
      addPolygons(color = "white", weight = 1, smoothFactor = 0.5,
                  opacity = 1.0, fillOpacity = 0.5,
                  dashArray = "3",
                  fillColor = ~pal(Pop_Densit), # the attribute Pop_Densit is encoded according to the given color palette
                  highlightOptions = highlightOptions(color = "grey", weight = 2,
                                                      bringToFront = TRUE)
      )

```

__Run the application to see the changes.__


#### Labels
The polygons are visible, but there are no labels on them. To include the labels we add two other arguments to the method `addPolygons`.

```{r eval=FALSE}
%>% # pipe operator
      addPolygons(color = "white", weight = 1, smoothFactor = 0.5,
                  opacity = 1.0, fillOpacity = 0.5,
                  dashArray = "3",
                  fillColor = ~pal(Pop_Densit), # the attribute Pop_Densit is encoded according to the given color palette
                  label = districts$DISTRICT, # Show the district name as label
                  labelOptions = labelOptions( # Here we can define the font characteristics and text size.
                    style = list("font-weight" = "normal", padding = "3px 8px"),
                    textsize = "15px",
                    direction = "auto"),
                  highlightOptions = highlightOptions(color = "grey", weight = 2,
                                                      bringToFront = TRUE)
      )
```

#### Legend

The Leaflet package includes convenience functions for creating color legends. In this section. Use the `addLegend` function to add a legend. The easiest way to use addLegend is to provide `pal` (a palette function, as generated from `colorNumeric` et al.) and values, and let it calculate the colors and labels for you.


```{r eval=FALSE}
%>% # pipe operator
      # Add legend.
      # When a color palette function is used in a map (e.g. colorNumeric), a color legend can be automatically derived from the palette           function. You can also manually specify the colors and labels for the legend.
      addLegend(pal = pal, values = ~Pop_Densit, opacity = 0.7, title = "Population density",
                position = "bottomleft")
```

__Run the application to see the changes.__

```{r echo=FALSE, out.width='100%'}
knitr::include_graphics('./fig/5_5app.jpg')
```

__Therefore, the file server.R should look like this:__

```{r eval=FALSE}

library(shiny)
library(leaflet)
library(rgdal)
library(sp)
# Define the data
districts <- readOGR("shp/Munich_Districts.shp",
                     layer = "Munich_Districts")
# Define the topics for the thematic maps and histogram according to the given SHP file attributes.				 
vars <- c(
  "Population density" = "Pop_Densit",
  "Inhabitants" = "Inhabitant",
  "Foreigners" = "Foreigners",
  "Households" = "Households"
)
shinyServer(function(input, output) {
  output$map <- renderLeaflet({
    pal <- colorNumeric("YlOrRd", domain = districts$colorData, n = 6)
    leaflet(districts) %>%
      # Base groups.
      # The tiles and provider tiles might be added to a group. Human-friendly group names are permitted-
      # they need not be short, identifier-style names.
      addTiles(group = "OpenStreetMap") %>%
      addProviderTiles("CartoDB.Positron", group = "CartoDB") %>%
      addProviderTiles("Esri.WorldImagery", group = "ESRI") %>%
      # Point of map view. Here we can set the coordinates and zoom
      setView(lng = 11.557137, lat = 48.157831, zoom = 12)%>%
      # Overview map
      # toggleDisplay sets whether the minimap should have a button to minimise it. Defaults to false.
      addMiniMap(tiles = providers$CartoDB.Positron,
                 toggleDisplay = TRUE)%>%  
      # Layers
      addLayersControl(
        baseGroups = c("OpenStreetMap", "CartoDB", "ESRI")
      )%>%
      addPolygons(color = "white", weight = 1, smoothFactor = 0.5,
                  opacity = 1.0, fillOpacity = 0.5,
                  dashArray = "3",
                  fillColor = ~pal(Pop_Densit), # the attribute Pop_Densit is encoded according to the given color palette
                  label = districts$DISTRICT, # Show the district name as label
                  labelOptions = labelOptions( # Here we can define the font characteristics and text size.
                    style = list("font-weight" = "normal", padding = "3px 8px"),
                    textsize = "15px",
                    direction = "auto"),
                  highlightOptions = highlightOptions(color = "grey", weight = 2,
                                                      bringToFront = TRUE)
      )%>%
      # Add legend.
      # When a color palette function is used in a map (e.g. colorNumeric), a color legend can be automatically derived from the palette function.
      # You can also manually specify the colors and labels for the legend.
      addLegend(pal = pal, values = ~Pop_Densit, opacity = 0.7, title = "Population density",
                position = "bottomleft")
  })
})

```

The current choropleth map shops Population Density Distribution. Though the assignment suggests creating an interactive application where themes of the choropleth maps might be manipulated according to the user input.


## Reactivity

It's easy to build interactive applications with Shiny, but to get the most out of it, you'll need to understand the reactive programming model used by Shiny.
In a Shiny application, the source typically is user input through a browser interface. For example, you might define the theme of the choropleth map you want to visualize.  Therefore, when the selects an item, types input, or clicks on a button, these actions will set values that are reactive sources. A reactive endpoint is usually something that appears in the user's browser window, such as a plot or a table of values.

In Shiny, there are three kinds of objects in reactive programming: reactive sources, reactive conductors, and reactive endpoints, which are represented with these symbols:
More details can be found here: https://shiny.rstudio.com/articles/reactivity-overview.html

```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/6app.jpg')
```

In a simple Shiny application, reactive sources are accessible through the input object, and reactive endpoints are accessible through the output object.

```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/7app.jpg')
```

The reactive expression can assist us to define a topic for the choropleth map by selection from the dropdown list and simultaneously update the thematic map and map legend according to the selected value.

#### Select Input

First of all, we need a list of topics in the UI. Therefore, we create an input control Select Input, where we define 3 arguments.

* `"color"` - `inputId`.	The input slot that will be used to access the value.
* `"Color"` - `label`.	Display label for the control, or NULL for no label.
* `vars` - `choices`.	List of values to select from.


```{r eval=FALSE}
    sidebarPanel(
      selectInput("color", "Color", vars)
    )
```

Providing `selectInput` we can get a list of the themes based on the variables we have defined.

```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/8app.jpg')
```

This input might be accessed in server side through the expression `input$color`.


##### __Assignment 1__

Using the reactive expression you can create a histogram of the Districts data.
It should include a list of the available topics, by selecting the topic the histogram will be updated simultaneously to visualize the distribution of the input.

```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/9app.jpg')
```

__Hint__:

* ui.R: Create selectInput and plotOutput in UI.
```{r eval=FALSE}
      selectInput("plot",...),
      plotOutput("Histplot", height = 200)
```

* server.R
Add renderPlot in Server.R To plot the data you should use function hist().
This task you should perform by yourself using examples discussed previously.
```{r eval=FALSE}
  output$Histplot <- renderPlot({

  })
```

You need to read the input as `input$plot`. The plot data might be accessed as `districts[[plotBy]]`
```{r eval=FALSE}
    plotBy <- input$plot
    plotData <- districts[[plotBy]]
```

Then you need to plot the histogram.
```{r eval=FALSE}
hist(plotData, ....)
```





#### Observer

The map is integrated within our application, though the interactivity is rather limited. The R shiny provides us with a great functionality to interact with the data through the user input. Here we would like to change the topics for our thematic map and select the topics from the given data.

```{r eval=FALSE}
  "Population density" = "Pop_Densit",
  "Inhabitants" = "Inhabitant",
  "Foreigners" = "Foreigners",
  "Households" = "Households"
```
Coming back to the __server.R__ We need to create a __observer__ from the given expression. An observer is like a reactive expression. It can read reactive values and call reactive expressions, and will automatically re-execute when those dependencies change. But unlike reactive expressions, it doesn't yield a result and can't be used as an input to other reactive expressions. Thus, observers are only useful for their side effects.

The observer can manipulate any input given through UI and the reactive inputs and expressions defined on the server side. In our observer, we will read the input selected in UI. Thus, we need to access the `selectInput` with the inputId `color`. To do so we specify input as `colorBy <- input$color`, Furthermore, we need to connect the data with the selected input via `colorData <- districts[[colorBy]]`. The color palette was defined locally, thus we need to specify it again `pal2`.

```{r eval=FALSE}

  output$map <- renderLeaflet({

  ..........

    })

  observe({
    colorBy <- input$color
    colorData <- districts[[colorBy]]
    pal2 <- colorNumeric("YlOrRd", districts$colorData, n = 6)


    })


```


###### Modifying Existing Maps with leafletProxy
Reactive inputs and expressions that affect the `renderLeaflet` expression will cause the entire map to be redrawn from scratch and reset the map position and zoom level. For some situations that may be acceptable or desirable behaviour. But in other situations, you may want finer-grained control over the map, such as changing the color of a single polygon or adding a marker at the point of a click - without redrawing the entire map.

To modify a map that's already running in the page, you use the `leafletProxy()` function in place of the `leaflet()` call, but otherwise, use Leaflet function calls as normal. Normally you use leaflet to create the static aspects of the map, and leafletProxy to manage the dynamic elements.

Thus, within our observer we can update Leaflet map using `leafletProxy("map", data = districts)`. Here we

`clearShapes()` remove one or more features from a map, identified by layerId; or, clear all features of the given type or group.

```{r eval=FALSE}

  observe({
    colorBy <- input$color
    colorData <- districts[[colorBy]]
    pal2 <- colorNumeric("YlOrRd", districts$colorData, n = 6)

    leafletProxy("map", data = districts) %>%
      clearShapes() %>%
      addPolygons(color = "white", weight = 1, smoothFactor = 0.5,
                  opacity = 1.0, fillOpacity = 0.5,
                  fillColor = ~pal2(colorData),
                  dashArray = "3",
                  highlightOptions = highlightOptions(color = "grey", weight = 2,
                                                      bringToFront = TRUE),
                  label = districts$DISTRICT,
                  labelOptions = labelOptions(
                    style = list("font-weight" = "normal", padding = "3px 8px"),
                    textsize = "15px",
                    direction = "auto")
      )

  })


```

##### __Assignment 2__

Update the Map Legend in the same way as the polygons. By changing the map topic, the legend should be changed as well.


##### __Assignment 3__ Optional

Correct the labels. Now the labels include only district names from the data. Include a second line so that there are district names and the number of inhabitants `districts$DISTRICT, districts$Inhabitant`.


```{r echo=FALSE, out.width='50%'}
knitr::include_graphics('./fig/10app.jpg')
```

Hint:
Define a new variable where you can specify the labels.
For example, sprintf() might be used.




## References and reading materials:
* https://cran.r-project.org/web/packages/leaflet/leaflet.pdf
* https://rstudio.github.io/leaflet/shiny.html
* https://shiny.rstudio.com/articles/basics.html
* https://shiny.rstudio.com/articles/reactivity-overview.html
* https://shiny.rstudio.com/reference/shiny/0.14/observe.html
* https://cran.r-project.org/web/packages/RColorBrewer/RColorBrewer.pdf




License: [CC-BY](https://creativecommons.org/licenses/by/3.0/)
