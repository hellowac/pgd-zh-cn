创建示例地图
============================================

Creating an example map

.. tab:: 中文

    To better understand how the various parts of Mapnik work together, let's write
    a simple Python program, which generates the map shown at the start of this
    chapter. This map makes use of the World Borders Dataset, which you downloaded
    in an earlier chapter; copy the TM_WORLD_BORDERS-0.3 shapefile directory into a
    convenient place, and create a new Python script in the same place. We'll call this
    program *createExampleMap.py*.

    .. note::

        Obviously, if you've gotten this far without downloading and installing Mapnik, you need to do so now. Mapnik can be found at http://mapnik.org..

    We'll start by importing the Mapnik toolkit and defining some constants, which the
    program will need:

    .. code-block:: python

        import mapnik
        
        MIN_LAT = -35
        MAX_LAT = +35
        MIN_LONG = -12
        MAX_LONG = +50
        
        MAP_WIDTH = 700
        MAP_HEIGHT = 800

    The MIN_LAT, MAX_LAT, MIN_LONG, and MAX_LONG constants define the lat/long
    coordinates for the portion of the world to display on the map, while the MAP_WIDTH
    and MAP_HEIGHT constants define the size of the generated map image, in pixels.
    Obviously, you can change these if you want.

    We're now ready to define the contents of the map. This map will have two layers,
    one for drawing the polygons and another for drawing the labels. We'll define a
    Mapnik Style object for each of these two layers. Let's start with the style for the
    "Polygons" layer:

    .. code-block:: python

        polygonStyle = mapnik.Style()

    As we discussed in the previous section, a filter object lets you choose which
    particular features a rule will apply to. In this case, we want to set up two rules,
    one to draw Angola in dark red, and another to draw all the other countries in
    dark green:

    .. code-block:: python

        rule = mapnik.Rule()
        rule.filter = mapnik.Filter("[NAME] = 'Angola'")
        symbol = mapnik.PolygonSymbolizer(mapnik.Color("#604040"))
        rule.symbols.append(symbol)

        polygonStyle.rules.append(rule)

        rule = mapnik.Rule()
        rule.filter = mapnik.Filter("[NAME] != 'Angola'")
        symbol = mapnik.PolygonSymbolizer(mapnik.Color("#406040"))
        rule.symbols.append(symbol)

        polygonStyle.rules.append(rule)

    Note how we create a PolygonSymbolizer to fill the country polygon in an
    appropriate color, and then add this symbolizer to our current rule. As we
    define the rules, we add them to our polygon style.

    Now that we've filled the country polygons, we'll define an additional rule to
    draw the polygon outlines:

    .. code-block:: python

        rule = mapnik.Rule()
        symbol = mapnik.LineSymbolizer(mapnik.Color("#000000"), 0.1)
        rule.symbols.append(symbol)

        polygonStyle.rules.append(rule)

    This is all that's required to display the country polygons onto the map. Let's now
    go ahead and define a second Mapnik Style object for the "Labels" layer:

    .. code-block:: python

        labelStyle = mapnik.Style()

        rule = mapnik.Rule()
        symbol = mapnik.TextSymbolizer(mapnik.Expression("[NAME]"),
                                       "DejaVu Sans Book", 12,
                                       mapnik.Color("#000000"))
        rule.symbols.append(symbol)

        labelStyle.rules.append(rule)

    This style uses a *TextSymbolizer* to draw the labels onto the map. Note that we
    create an Expression object to define the text to be displayed—in this case, we use the
    attribute called NAME from the shapefile; this attribute contains the name of the country.

    .. note::

        In this example, we are only using a single Mapnik style
        for each layer. When generating a more complex map, you
        will typically have a number of styles which can be applied
        to each layer, and styles may be shared between layers as
        appropriate. For this example, though, we are keeping the
        map definition as simple as possible.

    Now that we have set up our styles, we can start to define our map's layers.
    Before we do this, though, we need to set up our data source:

    .. code-block:: python

        datasource = mapnik.Shapefile(file="TM_WORLD_BORDERS-0.3/" +
                                           "TM_WORLD_BORDERS-0.3.shp")

    We can then define the two layers used by our map:

    .. code-block:: python

        polygonLayer = mapnik.Layer("Polygons")
        polygonLayer.datasource = datasource
        polygonLayer.styles.append("PolygonStyle")

        labelLayer = mapnik.Layer("Labels")
        labelLayer.datasource = datasource
        labelLayer.styles.append("LabelStyle")

    .. note::

        Note that we refer to styles by name, rather than inserting the style directly. This allows us to re-use styles, or to define styles in an XML definition file and then refer to them within our Python code. We'll add the styles definitions to our map shortly.

    We can now finally create our Map object. A Mapnik Map object has a size and
    projection, a background color, a list of styles, and a list of the layers that make
    up the map:

    .. code-block:: python

        map = mapnik.Map(MAP_WIDTH, MAP_HEIGHT,
                         "+proj=longlat +datum=WGS84")
        map.background = mapnik.Color("#8080a0")

        map.append_style("PolygonStyle", polygonStyle)
        map.append_style("LabelStyle", labelStyle)

        map.layers.append(polygonLayer)
        map.layers.append(labelLayer)

    The last thing we have to do is tell Mapnik to zoom in on the desired area of the
    world, and then render the map into an image file:

    .. code-block:: python

        map.zoom_to_box(mapnik.Box2d(MIN_LONG, MIN_LAT,
                                     MAX_LONG, MAX_LAT))
        mapnik.render_to_file(map, "map.png")

    If you run this program and open the map.png file, you will see the map you have generated:

    .. image:: ./img/312-0.png
       :class: with-border
       :align: center

    Obviously there's a lot more that you can do with Mapnik, but this example covers
    the main points and should be enough to let you started for generating your own
    maps. Make sure that you play with this example to become familiar with the way
    Mapnik works. Here are some things you might like to try:

    - Adjust the MIN_LAT, MIN_LONG, MAX_LAT, and MAX_LONG constants at the start of the program to zoom in on the country where you reside
    - Change the size of the generated image
    - Alter the map's colors
    - Add extra rules to display the country name in different font sizes and colors based on the country's population
    
    .. hint::

        To do this, you'll need to define filters that look like this::
    
            mapnik.Filter("[POP2005] > 1000000 and [POP2005] <= 2000000")

.. tab:: 英文

    To better understand how the various parts of Mapnik work together, let's write
    a simple Python program, which generates the map shown at the start of this
    chapter. This map makes use of the World Borders Dataset, which you downloaded
    in an earlier chapter; copy the TM_WORLD_BORDERS-0.3 shapefile directory into a
    convenient place, and create a new Python script in the same place. We'll call this
    program *createExampleMap.py*.

    .. note::

        Obviously, if you've gotten this far without downloading and installing Mapnik, you need to do so now. Mapnik can be found at http://mapnik.org..

    We'll start by importing the Mapnik toolkit and defining some constants, which the
    program will need:

    .. code-block:: python

        import mapnik
        
        MIN_LAT = -35
        MAX_LAT = +35
        MIN_LONG = -12
        MAX_LONG = +50
        
        MAP_WIDTH = 700
        MAP_HEIGHT = 800

    The MIN_LAT, MAX_LAT, MIN_LONG, and MAX_LONG constants define the lat/long
    coordinates for the portion of the world to display on the map, while the MAP_WIDTH
    and MAP_HEIGHT constants define the size of the generated map image, in pixels.
    Obviously, you can change these if you want.

    We're now ready to define the contents of the map. This map will have two layers,
    one for drawing the polygons and another for drawing the labels. We'll define a
    Mapnik Style object for each of these two layers. Let's start with the style for the
    "Polygons" layer:

    .. code-block:: python

        polygonStyle = mapnik.Style()

    As we discussed in the previous section, a filter object lets you choose which
    particular features a rule will apply to. In this case, we want to set up two rules,
    one to draw Angola in dark red, and another to draw all the other countries in
    dark green:

    .. code-block:: python

        rule = mapnik.Rule()
        rule.filter = mapnik.Filter("[NAME] = 'Angola'")
        symbol = mapnik.PolygonSymbolizer(mapnik.Color("#604040"))
        rule.symbols.append(symbol)

        polygonStyle.rules.append(rule)

        rule = mapnik.Rule()
        rule.filter = mapnik.Filter("[NAME] != 'Angola'")
        symbol = mapnik.PolygonSymbolizer(mapnik.Color("#406040"))
        rule.symbols.append(symbol)

        polygonStyle.rules.append(rule)

    Note how we create a PolygonSymbolizer to fill the country polygon in an
    appropriate color, and then add this symbolizer to our current rule. As we
    define the rules, we add them to our polygon style.

    Now that we've filled the country polygons, we'll define an additional rule to
    draw the polygon outlines:

    .. code-block:: python

        rule = mapnik.Rule()
        symbol = mapnik.LineSymbolizer(mapnik.Color("#000000"), 0.1)
        rule.symbols.append(symbol)

        polygonStyle.rules.append(rule)

    This is all that's required to display the country polygons onto the map. Let's now
    go ahead and define a second Mapnik Style object for the "Labels" layer:

    .. code-block:: python

        labelStyle = mapnik.Style()

        rule = mapnik.Rule()
        symbol = mapnik.TextSymbolizer(mapnik.Expression("[NAME]"),
                                       "DejaVu Sans Book", 12,
                                       mapnik.Color("#000000"))
        rule.symbols.append(symbol)

        labelStyle.rules.append(rule)

    This style uses a *TextSymbolizer* to draw the labels onto the map. Note that we
    create an Expression object to define the text to be displayed—in this case, we use the
    attribute called NAME from the shapefile; this attribute contains the name of the country.

    .. note::

        In this example, we are only using a single Mapnik style
        for each layer. When generating a more complex map, you
        will typically have a number of styles which can be applied
        to each layer, and styles may be shared between layers as
        appropriate. For this example, though, we are keeping the
        map definition as simple as possible.

    Now that we have set up our styles, we can start to define our map's layers.
    Before we do this, though, we need to set up our data source:

    .. code-block:: python

        datasource = mapnik.Shapefile(file="TM_WORLD_BORDERS-0.3/" +
                                           "TM_WORLD_BORDERS-0.3.shp")

    We can then define the two layers used by our map:

    .. code-block:: python

        polygonLayer = mapnik.Layer("Polygons")
        polygonLayer.datasource = datasource
        polygonLayer.styles.append("PolygonStyle")

        labelLayer = mapnik.Layer("Labels")
        labelLayer.datasource = datasource
        labelLayer.styles.append("LabelStyle")

    .. note::

        Note that we refer to styles by name, rather than inserting the style directly. This allows us to re-use styles, or to define styles in an XML definition file and then refer to them within our Python code. We'll add the styles definitions to our map shortly.

    We can now finally create our Map object. A Mapnik Map object has a size and
    projection, a background color, a list of styles, and a list of the layers that make
    up the map:

    .. code-block:: python

        map = mapnik.Map(MAP_WIDTH, MAP_HEIGHT,
                         "+proj=longlat +datum=WGS84")
        map.background = mapnik.Color("#8080a0")

        map.append_style("PolygonStyle", polygonStyle)
        map.append_style("LabelStyle", labelStyle)

        map.layers.append(polygonLayer)
        map.layers.append(labelLayer)

    The last thing we have to do is tell Mapnik to zoom in on the desired area of the
    world, and then render the map into an image file:

    .. code-block:: python

        map.zoom_to_box(mapnik.Box2d(MIN_LONG, MIN_LAT,
                                     MAX_LONG, MAX_LAT))
        mapnik.render_to_file(map, "map.png")

    If you run this program and open the map.png file, you will see the map you have generated:

    .. image:: ./img/312-0.png
       :class: with-border
       :align: center

    Obviously there's a lot more that you can do with Mapnik, but this example covers
    the main points and should be enough to let you started for generating your own
    maps. Make sure that you play with this example to become familiar with the way
    Mapnik works. Here are some things you might like to try:

    - Adjust the MIN_LAT, MIN_LONG, MAX_LAT, and MAX_LONG constants at the start of the program to zoom in on the country where you reside
    - Change the size of the generated image
    - Alter the map's colors
    - Add extra rules to display the country name in different font sizes and colors based on the country's population
    
    .. hint::

        To do this, you'll need to define filters that look like this::
    
            mapnik.Filter("[POP2005] > 1000000 and [POP2005] <= 2000000")