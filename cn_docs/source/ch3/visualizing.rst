可视化地理空间数据
============================================

Visualizing geospatial data

.. tab:: 中文

    It's very hard, if not impossible, to understand geospatial data unless it is turned
    into a visual form—that is, until it is rendered as an image of some sort. Converting
    geospatial data into images requires a suitable toolkit. While there are several such
    toolkits available, we will look at one in particular: Mapnik.

.. tab:: 英文

    It's very hard, if not impossible, to understand geospatial data unless it is turned
    into a visual form—that is, until it is rendered as an image of some sort. Converting
    geospatial data into images requires a suitable toolkit. While there are several such
    toolkits available, we will look at one in particular: Mapnik.

Mapnik
----------

Mapnik

.. tab:: 中文

    Mapnik is a freely-available toolkit for building mapping applications. It takes
    geospatial data from a PostGIS database, shapefile, or any other format supported
    by GDAL/OGR, and turns it into clearly-rendered, good-looking images.

    There are a lot of complex issues involved in rendering maps well, and Mapnik does
    a good job of allowing the application developer to control the rendering process.
    Rules control which features should appear on the map, while "symbolizers" control
    the visual appearance of these features.

    Mapnik allows developers to create XML stylesheets that control the map-creation
    process. Just as with CSS stylesheets, Mapnik's stylesheets allow you complete
    control over the way geospatial data is rendered. Alternatively, you can create
    your styles by hand if you prefer.

    Mapnik itself is written in C++, though bindings are included which allow access
    to almost all of the Mapnik functionality via Python. Because these bindings are
    included in the main code base rather than being added by a third party developer,
    support for Python is built right into Mapnik. This makes Python eminently suited
    to developing Mapnik-based applications.

    Mapnik is heavily used by OpenStreetMap (http://openstreetmap.org),
    EveryBlock (http://everyblock.com), among others. Since the output of Mapnik
    is simply an image, it is easy to include Mapnik as part of a web-based application,
    or you can display the output directly in a window as part of a desktop-based
    application. Mapnik works equally well on the desktop and on the web.

.. tab:: 英文

    Mapnik is a freely-available toolkit for building mapping applications. It takes
    geospatial data from a PostGIS database, shapefile, or any other format supported
    by GDAL/OGR, and turns it into clearly-rendered, good-looking images.

    There are a lot of complex issues involved in rendering maps well, and Mapnik does
    a good job of allowing the application developer to control the rendering process.
    Rules control which features should appear on the map, while "symbolizers" control
    the visual appearance of these features.

    Mapnik allows developers to create XML stylesheets that control the map-creation
    process. Just as with CSS stylesheets, Mapnik's stylesheets allow you complete
    control over the way geospatial data is rendered. Alternatively, you can create
    your styles by hand if you prefer.

    Mapnik itself is written in C++, though bindings are included which allow access
    to almost all of the Mapnik functionality via Python. Because these bindings are
    included in the main code base rather than being added by a third party developer,
    support for Python is built right into Mapnik. This makes Python eminently suited
    to developing Mapnik-based applications.

    Mapnik is heavily used by OpenStreetMap (http://openstreetmap.org),
    EveryBlock (http://everyblock.com), among others. Since the output of Mapnik
    is simply an image, it is easy to include Mapnik as part of a web-based application,
    or you can display the output directly in a window as part of a desktop-based
    application. Mapnik works equally well on the desktop and on the web.


设计
----------

Design

.. tab:: 中文

    When using Mapnik, the main object you are dealing with is called the Map. A Map object has the following parts:

    .. image:: ./img/89-0.png
       :align: center
       :class: with-border
       :scale: 70

    When creating a Map object, you assign values for the following:

    - The overall **width** and **height** of the map, in pixels.
    - The **spatial reference** to use for the map.
    - The **background color** to draw behind the contents of the map.

    You then define one or more Layers which hold the map's contents. Each Layer has the following:

    - A **name**.
    - A **Datasource** object defining where to get the data for this layer from. The Datasource can be a reference to a database, or it can be a shapefile or other GDAL/OGR data source.
    - A **spatial reference** to use for this layer. This can be different from the spatial reference used by the map as a whole, if appropriate.
    - A list of **styles** to apply to this layer. Each style is referred to by name, since the styles are actually defined elsewhere (often in an XML stylesheet).

    Finally, you define one or more **Styles**, which tell Mapnik how to draw the various layers. Each Style has a **name** and of a list of *Rules*, which make up the main part of the style's definition. Each *Rule* has:

    - A **minimum scale** and **maximum scale** value (called the "scale denominator"). The *Rule* will only apply if the map's scale is within this range.
    - A **filter** expression. The *Rule* will only apply to those features which match this filter expression.
    - A list of **Symbolizers**. These define how the matching features will be drawn onto the map.

    There are a number of different types of Symbolizers implemented by Mapnik:

    - *LineSymbolizer* is used to draw a "stroke" along a line, a linear ring, or around the outside of a polygon.
    - *LinePatternSymbolizer* uses the contents of an image file (specified by name) to draw the "stroke" along a line, a linear ring, or around the outside of a polygon.
    - *PolygonSymbolizer* is used to draw the interior of a polygon.
    - *PolygonPatternSymbolizer* uses the contents of an image file (again specified by name) to draw the interior of a polygon.
    - *PointSymbolizer* uses the contents of an image file (specified by name) to draw an image at a point.
    - *TextSymbolizer* draws a feature's text. The text to be drawn is taken from one of the feature's attributes, and there are numerous options to control how the text is to be drawn.
    - *RasterSymbolizer* is used to draw raster data taken from any GDAL dataset.
    - *ShieldSymbolizer* draws a textual label and a point together. This is similar to the use of a *PointSymbolizer* to draw the image and a *TextSymbolizer* to draw the label, except that it ensures that both the text and the image are drawn together.
    - *BuildingSymbolizer* uses a pseudo-3D effect to draw a polygon, to make it appear that the polygon is a three-dimensional building.
    - *MarkersSymbolizer* draws blue directional arrows or SVG markers following the direction of polygon and line geometries.

    When you instantiate a Symbolizer and add it to a style (either directly in code, or via an XML stylesheet), you provide a number of parameters which define how the Symbolizer should work. For example, when using the *PolygonSymbolizer*, you can specify the fill color, the opacity, and a "gamma" value that helps draw adjacent polygons of the same color without the boundary being shown::

        p = mapnik.PolygonSymbolizer(mapnik.Color(127, 127, 0))
        p.fill_opacity = 0.8
        p.gamma = 0.65

    If the Rule that uses this Symbolizer matches one or more polygons, those polygons will be drawn using the given color, opacity, and gamma value.

    Different rules can, of course, have different Symbolizers, as well as different filter values. For example, you might set up rules which draw countries in different colors depending on their population.

.. tab:: 英文

    When using Mapnik, the main object you are dealing with is called the Map. A Map object has the following parts:

    .. image:: ./img/89-0.png
       :align: center
       :class: with-border
       :scale: 70

    When creating a Map object, you assign values for the following:

    - The overall **width** and **height** of the map, in pixels.
    - The **spatial reference** to use for the map.
    - The **background color** to draw behind the contents of the map.

    You then define one or more Layers which hold the map's contents. Each Layer has the following:

    - A **name**.
    - A **Datasource** object defining where to get the data for this layer from. The Datasource can be a reference to a database, or it can be a shapefile or other GDAL/OGR data source.
    - A **spatial reference** to use for this layer. This can be different from the spatial reference used by the map as a whole, if appropriate.
    - A list of **styles** to apply to this layer. Each style is referred to by name, since the styles are actually defined elsewhere (often in an XML stylesheet).

    Finally, you define one or more **Styles**, which tell Mapnik how to draw the various layers. Each Style has a **name** and of a list of *Rules*, which make up the main part of the style's definition. Each *Rule* has:

    - A **minimum scale** and **maximum scale** value (called the "scale denominator"). The *Rule* will only apply if the map's scale is within this range.
    - A **filter** expression. The *Rule* will only apply to those features which match this filter expression.
    - A list of **Symbolizers**. These define how the matching features will be drawn onto the map.

    There are a number of different types of Symbolizers implemented by Mapnik:

    - *LineSymbolizer* is used to draw a "stroke" along a line, a linear ring, or around the outside of a polygon.
    - *LinePatternSymbolizer* uses the contents of an image file (specified by name) to draw the "stroke" along a line, a linear ring, or around the outside of a polygon.
    - *PolygonSymbolizer* is used to draw the interior of a polygon.
    - *PolygonPatternSymbolizer* uses the contents of an image file (again specified by name) to draw the interior of a polygon.
    - *PointSymbolizer* uses the contents of an image file (specified by name) to draw an image at a point.
    - *TextSymbolizer* draws a feature's text. The text to be drawn is taken from one of the feature's attributes, and there are numerous options to control how the text is to be drawn.
    - *RasterSymbolizer* is used to draw raster data taken from any GDAL dataset.
    - *ShieldSymbolizer* draws a textual label and a point together. This is similar to the use of a *PointSymbolizer* to draw the image and a *TextSymbolizer* to draw the label, except that it ensures that both the text and the image are drawn together.
    - *BuildingSymbolizer* uses a pseudo-3D effect to draw a polygon, to make it appear that the polygon is a three-dimensional building.
    - *MarkersSymbolizer* draws blue directional arrows or SVG markers following the direction of polygon and line geometries.

    When you instantiate a Symbolizer and add it to a style (either directly in code, or via an XML stylesheet), you provide a number of parameters which define how the Symbolizer should work. For example, when using the *PolygonSymbolizer*, you can specify the fill color, the opacity, and a "gamma" value that helps draw adjacent polygons of the same color without the boundary being shown::

        p = mapnik.PolygonSymbolizer(mapnik.Color(127, 127, 0))
        p.fill_opacity = 0.8
        p.gamma = 0.65

    If the Rule that uses this Symbolizer matches one or more polygons, those polygons will be drawn using the given color, opacity, and gamma value.

    Different rules can, of course, have different Symbolizers, as well as different filter values. For example, you might set up rules which draw countries in different colors depending on their population.


示例代码
----------

Example code

.. tab:: 中文

    The following example program displays a simple world map using Mapnik:

    .. code-block:: python

        import mapnik

        symbolizer = mapnik.PolygonSymbolizer(mapnik.Color("darkgreen"))

        rule = mapnik.Rule()
        rule.symbols.append(symbolizer)

        style = mapnik.Style()
        style.rules.append(rule)

        layer = mapnik.Layer("mapLayer")
        layer.datasource = mapnik.Shapefile(file="TM_WORLD_BORDERS-0.3.shp")
        layer.styles.append("mapStyle")

        map = mapnik.Map(800, 400)
        map.background = mapnik.Color("steelblue")
        map.append_style("mapStyle", style)
        map.layers.append(layer)

        map.zoom_all()
        mapnik.render_to_file(map, "map.png", "png")

    .. note::

        If you are running Mapnik Version 2.0, you should replace the import mapnik statement in the first line of this program with import mapnik2 as mapnik.

    Notice that this program creates a ``PolygonSymbolizer`` to display the country
    polygons, and then attaches the symbolizer to a Mapnik Rule object. The Rule
    then becomes part of a Mapnik ``Style`` object. We then create a Mapnik Layer object,
    reading the layer's map data from a shapefile data source. Finally, a Mapnik Map
    object is created, the layer is attached, and the resulting map is rendered to a
    PNG-format image file:

    .. image:: ./img/92-0.png
       :align: center
       :class: with-border
       :scale: 90

.. tab:: 英文

    The following example program displays a simple world map using Mapnik:

    .. code-block:: python

        import mapnik

        symbolizer = mapnik.PolygonSymbolizer(mapnik.Color("darkgreen"))

        rule = mapnik.Rule()
        rule.symbols.append(symbolizer)

        style = mapnik.Style()
        style.rules.append(rule)

        layer = mapnik.Layer("mapLayer")
        layer.datasource = mapnik.Shapefile(file="TM_WORLD_BORDERS-0.3.shp")
        layer.styles.append("mapStyle")

        map = mapnik.Map(800, 400)
        map.background = mapnik.Color("steelblue")
        map.append_style("mapStyle", style)
        map.layers.append(layer)

        map.zoom_all()
        mapnik.render_to_file(map, "map.png", "png")

    .. note::

        If you are running Mapnik Version 2.0, you should replace the import mapnik statement in the first line of this program with import mapnik2 as mapnik.

    Notice that this program creates a ``PolygonSymbolizer`` to display the country
    polygons, and then attaches the symbolizer to a Mapnik Rule object. The Rule
    then becomes part of a Mapnik ``Style`` object. We then create a Mapnik Layer object,
    reading the layer's map data from a shapefile data source. Finally, a Mapnik Map
    object is created, the layer is attached, and the resulting map is rendered to a
    PNG-format image file:

    .. image:: ./img/92-0.png
       :align: center
       :class: with-border
       :scale: 90

文档
----------

Documentation

.. tab:: 中文

    Mapnik's has reasonable documentation for an open source project: there are good
    installation guides and some excellent tutorials, but the API documentation is often
    confusing. The Python documentation is derived from the C++ documentation, and
    concentrates on describing how the Python bindings are implemented rather than
    how an end user would work with Mapnik using Python—there's a lot of technical
    details that aren't relevant to the Python programmer, and many Python-specific
    descriptions are missing.

    The best way to get started with Mapnik is to follow the installation instructions,
    and then to work your way through the supplied Python-specific tutorial. You can
    then check out the Learning Mapnik page on the Mapnik Wiki:

    http://trac.mapnik.org/wiki/LearningMapnik

    It is well worth spending some time reading through the Mapnik Wiki, even
    though not all of it is Python-specific. It is also a good idea to look at the Python API
    documentation, despite its limitations. The main page lists the various classes, which
    are available and a number of useful functions, many of which are documented. The
    classes themselves list the methods and properties (attributes) you can access, and
    even though many of these lack Python-specific documentation, you can generally
    guess what they do.

    .. note::

        *Chapter 8, Using Python and Mapnik to Produce Maps*, of this book includes a comprehensive description of Mapnik and how to use it from Python; you may find this more useful than the Python API documentation on the Mapnik website.

.. tab:: 英文

    Mapnik's has reasonable documentation for an open source project: there are good
    installation guides and some excellent tutorials, but the API documentation is often
    confusing. The Python documentation is derived from the C++ documentation, and
    concentrates on describing how the Python bindings are implemented rather than
    how an end user would work with Mapnik using Python—there's a lot of technical
    details that aren't relevant to the Python programmer, and many Python-specific
    descriptions are missing.

    The best way to get started with Mapnik is to follow the installation instructions,
    and then to work your way through the supplied Python-specific tutorial. You can
    then check out the Learning Mapnik page on the Mapnik Wiki:

    http://trac.mapnik.org/wiki/LearningMapnik

    It is well worth spending some time reading through the Mapnik Wiki, even
    though not all of it is Python-specific. It is also a good idea to look at the Python API
    documentation, despite its limitations. The main page lists the various classes, which
    are available and a number of useful functions, many of which are documented. The
    classes themselves list the methods and properties (attributes) you can access, and
    even though many of these lack Python-specific documentation, you can generally
    guess what they do.

    .. note::

        *Chapter 8, Using Python and Mapnik to Produce Maps*, of this book includes a comprehensive description of Mapnik and how to use it from Python; you may find this more useful than the Python API documentation on the Mapnik website.


可用性
----------

Availability

.. tab:: 中文

    Mapnik runs on all major operating systems, including MS Windows, Mac OS X,
    and Linux. The main Mapnik website can be found at:

    http://mapnik.org

    Download links are provided for downloading the Mapnik source code, which
    can be readily compiled if you are running on a Unix machine, and you can also
    download prebuilt binaries for Windows and Mac OS X.

    .. note::

        Make sure that you install Mapnik Version 2.0 or later; you will need to use this version as you work through the examples in this book.

.. tab:: 英文

    Mapnik runs on all major operating systems, including MS Windows, Mac OS X,
    and Linux. The main Mapnik website can be found at:

    http://mapnik.org

    Download links are provided for downloading the Mapnik source code, which
    can be readily compiled if you are running on a Unix machine, and you can also
    download prebuilt binaries for Windows and Mac OS X.

    .. note::

        Make sure that you install Mapnik Version 2.0 or later; you will need to use this version as you work through the examples in this book.
