重新审视 MapGenerator
============================================

MapGenerator revisited

.. tab:: 中文

    Now that we have examined the Python interface to Mapnik, let's use this
    knowledge to take a closer look at the mapGenerator.py module used in Chapter
    7, Working with Spatial Data. As well as being a more comprehensive example of
    creating maps programmatically, the mapGenerator.py module suggests ways in
    which you can write your own wrapper around Mapnik to simplify the creation
    of a map using Python code.

.. tab:: 英文

    Now that we have examined the Python interface to Mapnik, let's use this
    knowledge to take a closer look at the mapGenerator.py module used in Chapter
    7, Working with Spatial Data. As well as being a more comprehensive example of
    creating maps programmatically, the mapGenerator.py module suggests ways in
    which you can write your own wrapper around Mapnik to simplify the creation
    of a map using Python code.


MapGenerator 接口
-------------------------------------
The MapGenerator interface

.. tab:: 中文

    The mapGenerator.py module defines just one function, generateMap(), which
    allows you to create a simple map which is stored in a temporary file on disk.
    The method signature for the generateMap() function looks like this::

        def generateMap(datasource, minX, minY, maxX, maxY,
                        mapWidth, mapHeight,
                        hiliteExpr=None, background="#8080a0",
                        hiliteLine="#000000", hiliteFill="#408000",
                        normalLine="#404040", normalFill="#a0a0a0",
                        points=None)

    The parameters are as follows:

    - *datasource* is a dictionary defining the data source to use for this map. This dictionary should have at least one entry, type, which defines the type of data source. The following data source types are supported: "OGR", "PostGIS" and "SQLite". Any additional entries in this dictionary will be passed as keyword parameters to the data source initializer.
    - *minX*, *minY*, *maxX*, and *maxY* define the bounding box for the area to display, in map coordinates.
    - *mapWidth* and *mapHeight* are the width and height of the image to generate, in pixels.
    - *hiliteExpr* is a Mapnik filter expression to use to identify the feature(s) to be highlighted.
    - *background* is the HTML color code to use for the background of the map.
    - *hiliteLine* and *hiliteFill* are the HTML color codes to use for the line and fill for the highlighted features.
    - *normalLine* and *normalFill* are the HTML color codes to use for the line and fill for the non-highlighted features.
    - *points*, if defined, should be a list of (long, lat, name) tuples identifying points to display on the map.

    Because many of these keyword parameters have default values, creating a simple map only requires the data source, bounding box, and map dimensions to be specified. Everything else is optional.

    The *generateMap()* function creates a new map based on the given parameters, and
    stores the result as a PNG format image file in a temporary map cache directory. Upon
    completion, it returns the name and relative path to the newly-rendered image file.

    So much for the public interface to the mapGenerator.py module. Let's take a look inside to see how it works.

.. tab:: 英文

    The mapGenerator.py module defines just one function, generateMap(), which
    allows you to create a simple map which is stored in a temporary file on disk.
    The method signature for the generateMap() function looks like this::

        def generateMap(datasource, minX, minY, maxX, maxY,
                        mapWidth, mapHeight,
                        hiliteExpr=None, background="#8080a0",
                        hiliteLine="#000000", hiliteFill="#408000",
                        normalLine="#404040", normalFill="#a0a0a0",
                        points=None)

    The parameters are as follows:

    - *datasource* is a dictionary defining the data source to use for this map. This dictionary should have at least one entry, type, which defines the type of data source. The following data source types are supported: "OGR", "PostGIS" and "SQLite". Any additional entries in this dictionary will be passed as keyword parameters to the data source initializer.
    - *minX*, *minY*, *maxX*, and *maxY* define the bounding box for the area to display, in map coordinates.
    - *mapWidth* and *mapHeight* are the width and height of the image to generate, in pixels.
    - *hiliteExpr* is a Mapnik filter expression to use to identify the feature(s) to be highlighted.
    - *background* is the HTML color code to use for the background of the map.
    - *hiliteLine* and *hiliteFill* are the HTML color codes to use for the line and fill for the highlighted features.
    - *normalLine* and *normalFill* are the HTML color codes to use for the line and fill for the non-highlighted features.
    - *points*, if defined, should be a list of (long, lat, name) tuples identifying points to display on the map.

    Because many of these keyword parameters have default values, creating a simple map only requires the data source, bounding box, and map dimensions to be specified. Everything else is optional.

    The *generateMap()* function creates a new map based on the given parameters, and
    stores the result as a PNG format image file in a temporary map cache directory. Upon
    completion, it returns the name and relative path to the newly-rendered image file.

    So much for the public interface to the mapGenerator.py module. Let's take a look inside to see how it works.


创建主地图层
-------------------------------------
Creating the main map layer

.. tab:: 中文

    The module starts by creating a mapnik.Map object to hold the generated map.
    We set the background color at the same time::
        
        map = mapnik.Map(mapWidth, mapHeight,
                        '+proj=longlat +datum=WGS84')
        map.background_color = mapnik.Color(background)

    We next have to set up the Mapnik data source to load our map data from.
    To simplify the job of accessing a data source, the datasource parameter includes
    the type of data source, as well as any additional entries which are passed as
    keyword parameters directly to the Mapnik data source initializer::

        srcType = datasource['type']
        del datasource['type']

        if srcType == "OGR":
            source = mapnik.Ogr(**datasource)
        elif srcType == "PostGIS":
            source = mapnik.PostGIS(**datasource)
        elif srcType == "SQLite":
            source = mapnik.SQLite(**datasource)

    We then create our Layer object, and start defining the style, which is used to draw
    the map data onto the map::

        layer = mapnik.Layer("Layer")
        layer.datasource = source

        style = mapnik.Style()

    We next set up a rule that only applies to the highlighted features::

        rule = mapnik.Rule()
        if hiliteExpr != None:
            rule.filter = mapnik.Filter(hiliteExpr)

    This rule will use the "highlight" line and fill colors::

        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(hiliteFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(hiliteLine), 0.1)))

    We then add this rule to the style, and create another rule that only applies to the non-highlighted features::

        style.rules.append(rule)

        rule = mapnik.Rule()
        rule.set_else(True)

    This rule will use the "normal" line and fill colors::

        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(normalFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(normalLine), 0.1)))

    We then add this rule to the style, and add the style to the map and layer::

        style.rules.append(rule)

        map.append_style("Map Style", style)
        layer.styles.append("Map Style")

    Finally, the layer is added to the map::
    
        map.layers.append(layer)

.. tab:: 英文

    The module starts by creating a mapnik.Map object to hold the generated map.
    We set the background color at the same time::
        
        map = mapnik.Map(mapWidth, mapHeight,
                        '+proj=longlat +datum=WGS84')
        map.background_color = mapnik.Color(background)

    We next have to set up the Mapnik data source to load our map data from.
    To simplify the job of accessing a data source, the datasource parameter includes
    the type of data source, as well as any additional entries which are passed as
    keyword parameters directly to the Mapnik data source initializer::

        srcType = datasource['type']
        del datasource['type']

        if srcType == "OGR":
            source = mapnik.Ogr(**datasource)
        elif srcType == "PostGIS":
            source = mapnik.PostGIS(**datasource)
        elif srcType == "SQLite":
            source = mapnik.SQLite(**datasource)

    We then create our Layer object, and start defining the style, which is used to draw
    the map data onto the map::

        layer = mapnik.Layer("Layer")
        layer.datasource = source

        style = mapnik.Style()

    We next set up a rule that only applies to the highlighted features::

        rule = mapnik.Rule()
        if hiliteExpr != None:
            rule.filter = mapnik.Filter(hiliteExpr)

    This rule will use the "highlight" line and fill colors::

        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(hiliteFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(hiliteLine), 0.1)))

    We then add this rule to the style, and create another rule that only applies to the non-highlighted features::

        style.rules.append(rule)

        rule = mapnik.Rule()
        rule.set_else(True)

    This rule will use the "normal" line and fill colors::

        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(normalFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(normalLine), 0.1)))

    We then add this rule to the style, and add the style to the map and layer::

        style.rules.append(rule)
        
        map.append_style("Map Style", style)
        layer.styles.append("Map Style")

    Finally, the layer is added to the map::
    
        map.layers.append(layer)


在地图上显示点
-------------------------------------
Displaying points on the map

.. tab:: 中文

    One of the features of the generateMap() function is that it can take a list of points
    and display them directly onto the map without having to store those points into a
    database. This is done through the use of a MemoryDataSource data source and a
    ShieldSymbolizer to draw the points onto the map::

        if points != None:
            memoryDatasource = mapnik.MemoryDatasource()
            context = mapnik.Context()
            context.push("name")
            next_id = 1
            for long,lat,name in points:
                wkt = "POINT (%0.8f %0.8f)" % (long,lat)
                feature = mapnik.Feature(context, next_id)
                feature['name'] = name
                feature.add_geometries_from_wkt(wkt)
                next_id = next_id + 1
                memoryDatasource.add_feature(feature)

                layer = mapnik.Layer("Points")
                layer.datasource = memoryDatasource

                style = mapnik.Style()
                rule = mapnik.Rule()

                pointImgFile = os.path.join(os.path.dirname(__file__),
                                            "point.png")

                shield = mapnik.ShieldSymbolizer(
                                mapnik.Expression('[name]'),
                                "DejaVu Sans Bold", 10,
                                mapnik.Color("#000000"),
                                mapnik.PathExpression(pointImgFile))
                shield.displacement = (0, 7)
                shield.unlock_image = True
                rule.symbols.append(shield)

                style.rules.append(rule)

                map.append_style("Point Style", style)
                layer.styles.append("Point Style")
                
                map.layers.append(layer)

    .. note::

        Note that the path to the point.png file is calculated as an absolute path, based on the location of the mapGenerator.py module itself (via the __file__ global). This is done because the module can be called as part of a CGI script, and CGI scripts do not have a current working directory.

.. tab:: 英文

    One of the features of the generateMap() function is that it can take a list of points
    and display them directly onto the map without having to store those points into a
    database. This is done through the use of a MemoryDataSource data source and a
    ShieldSymbolizer to draw the points onto the map::

        if points != None:
            memoryDatasource = mapnik.MemoryDatasource()
            context = mapnik.Context()
            context.push("name")
            next_id = 1
            for long,lat,name in points:
                wkt = "POINT (%0.8f %0.8f)" % (long,lat)
                feature = mapnik.Feature(context, next_id)
                feature['name'] = name
                feature.add_geometries_from_wkt(wkt)
                next_id = next_id + 1
                memoryDatasource.add_feature(feature)

                layer = mapnik.Layer("Points")
                layer.datasource = memoryDatasource

                style = mapnik.Style()
                rule = mapnik.Rule()

                pointImgFile = os.path.join(os.path.dirname(__file__),
                                            "point.png")

                shield = mapnik.ShieldSymbolizer(
                                mapnik.Expression('[name]'),
                                "DejaVu Sans Bold", 10,
                                mapnik.Color("#000000"),
                                mapnik.PathExpression(pointImgFile))
                shield.displacement = (0, 7)
                shield.unlock_image = True
                rule.symbols.append(shield)

                style.rules.append(rule)

                map.append_style("Point Style", style)
                layer.styles.append("Point Style")
                
                map.layers.append(layer)

    .. note::

        Note that the path to the point.png file is calculated as an absolute path, based on the location of the mapGenerator.py module itself (via the __file__ global). This is done because the module can be called as part of a CGI script, and CGI scripts do not have a current working directory.


渲染地图
-------------------------------------
Rendering the map

.. tab:: 中文

    Because the mapGenerator.py module is designed to be used within a CGI script,
    the module makes use of a temporary map cache to hold the generated image files.
    Before it can render the map image, the generateMap() function has to create the
    map cache if it doesn't already exist, and create a temporary file within the cache
    directory to hold the generated map::
        
        scriptDir = os.path.dirname(__file__)
        cacheDir = os.path.join(scriptDir, "..", "mapCache")
        if not os.path.exists(cacheDir):
            os.mkdir(cacheDir)
        fd,filename = tempfile.mkstemp(".png", dir=cacheDir)
        os.close(fd)

    Finally, we are ready to render the map into an image file, and return back to the
    caller the relative path to the generated map file::

        map.zoom_to_box(mapnik.Box2d(minX, minY, maxX, maxY))
        mapnik.render_to_file(map, filename, "png")

        return "../mapCache/" + os.path.basename(filename)

.. tab:: 英文

    Because the mapGenerator.py module is designed to be used within a CGI script,
    the module makes use of a temporary map cache to hold the generated image files.
    Before it can render the map image, the generateMap() function has to create the
    map cache if it doesn't already exist, and create a temporary file within the cache
    directory to hold the generated map::
        
        scriptDir = os.path.dirname(__file__)
        cacheDir = os.path.join(scriptDir, "..", "mapCache")
        if not os.path.exists(cacheDir):
            os.mkdir(cacheDir)
        fd,filename = tempfile.mkstemp(".png", dir=cacheDir)
        os.close(fd)

    Finally, we are ready to render the map into an image file, and return back to the
    caller the relative path to the generated map file::

        map.zoom_to_box(mapnik.Box2d(minX, minY, maxX, maxY))
        mapnik.render_to_file(map, filename, "png")
        
        return "../mapCache/" + os.path.basename(filename)


地图生成器教给我们什么
-------------------------------------
What the map generator teaches us

.. tab:: 中文

    While in many ways the *mapGenerator.py* module is quite simplistic and designed
    specifically to meet the needs of the DISTAL application presented in the previous
    chapter, it is worth examining this module in depth because it shows how the principle
    of *encapsulation* can be used to hide Mapnik's complexity and simplify the process of
    map generation. Using the *generateMap()* function is infinitely easier than creating
    all the data sources, layers, symbolizers, rules, and styles each time a map has to
    be generated.

    It would be a relatively easy task to design a more generic map generator that could
    handle a variety of data sources and map layers, as well as various ways of returning
    the results, without having to exhaustively define every object by hand. Designing
    and implementing such a module would be very worthwhile if you want to use
    Mapnik extensively from your Python programs. Hopefully this section has given
    you some ideas about how you can proceed with implementing your own high-level
    Mapnik wrapper module.

.. tab:: 英文

    While in many ways the *mapGenerator.py* module is quite simplistic and designed
    specifically to meet the needs of the DISTAL application presented in the previous
    chapter, it is worth examining this module in depth because it shows how the principle
    of *encapsulation* can be used to hide Mapnik's complexity and simplify the process of
    map generation. Using the *generateMap()* function is infinitely easier than creating
    all the data sources, layers, symbolizers, rules, and styles each time a map has to
    be generated.

    It would be a relatively easy task to design a more generic map generator that could
    handle a variety of data sources and map layers, as well as various ways of returning
    the results, without having to exhaustively define every object by hand. Designing
    and implementing such a module would be very worthwhile if you want to use
    Mapnik extensively from your Python programs. Hopefully this section has given
    you some ideas about how you can proceed with implementing your own high-level
    Mapnik wrapper module.

