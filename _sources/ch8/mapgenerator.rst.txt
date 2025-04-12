重新审视 MapGenerator
============================================

MapGenerator revisited

.. tab:: 中文

    现在我们已经了解了 Mapnik 的 Python 接口，让我们利用这些知识更深入地研究在第 7 章《处理空间数据》中使用的 `mapGenerator.py` 模块。除了作为一个更全面的创建地图的示例外， `mapGenerator.py` 模块还建议了如何围绕 Mapnik 编写自己的封装器，以简化使用 Python 代码创建地图的过程。

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

    `mapGenerator.py` 模块定义了一个函数 `generateMap()`，该函数允许您创建一个简单的地图，并将其存储在磁盘上的临时文件中。 `generateMap()` 函数的签名如下::

        def generateMap(datasource, minX, minY, maxX, maxY,
                        mapWidth, mapHeight,
                        hiliteExpr=None, background="#8080a0",
                        hiliteLine="#000000", hiliteFill="#408000",
                        normalLine="#404040", normalFill="#a0a0a0",
                        points=None)

    各个参数说明如下：

    - **datasource** 是一个定义用于此地图的数据源的字典。该字典至少应包含一个条目 `type`，它定义了数据源的类型。支持的以下数据源类型有："OGR"、"PostGIS" 和 "SQLite"。此字典中的任何附加条目将作为关键字参数传递给数据源初始化函数。
    - **minX**, **minY**, **maxX**, **maxY** 定义了要显示区域的边界框，单位为地图坐标。
    - **mapWidth** 和 **mapHeight** 是生成的地图图像的宽度和高度，单位为像素。
    - **hiliteExpr** 是一个 Mapnik 过滤器表达式，用于标识需要高亮显示的要素。
    - **background** 是用于地图背景的 HTML 颜色代码。
    - **hiliteLine** 和 **hiliteFill** 分别是高亮要素的边界线和填充颜色的 HTML 颜色代码。
    - **normalLine** 和 **normalFill** 分别是非高亮要素的边界线和填充颜色的 HTML 颜色代码。
    - **points** 是一个由 (经度, 纬度, 名称) 元组组成的列表，标识要在地图上显示的点。

    由于许多这些关键字参数都有默认值，因此只需要指定数据源、边界框和地图尺寸即可创建一个简单的地图，其他参数则是可选的。

    `generateMap()` 函数会根据给定的参数创建一个新的地图，并将结果作为 PNG 格式的图像文件存储在临时的地图缓存目录中。完成后，它返回新生成的图像文件的名称和相对路径。

    到这里，我们了解了 `mapGenerator.py` 模块的公共接口，接下来让我们深入看看它是如何实现的。

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

    该模块首先创建了一个 `mapnik.Map` 对象，用于保存生成的地图，并同时设置背景颜色::

        map = mapnik.Map(mapWidth, mapHeight, '+proj=longlat +datum=WGS84')
        map.background_color = mapnik.Color(background)

    接下来，我们需要设置 Mapnik 数据源来加载地图数据。为了简化访问数据源的工作，`datasource` 参数包括了数据源的类型，以及任何其他额外的条目，这些额外条目会作为关键字参数直接传递给 Mapnik 数据源初始化函数::

        srcType = datasource['type']
        del datasource['type']

        if srcType == "OGR":
            source = mapnik.Ogr(**datasource)
        elif srcType == "PostGIS":
            source = mapnik.PostGIS(**datasource)
        elif srcType == "SQLite":
            source = mapnik.SQLite(**datasource)

    然后，我们创建一个 `Layer` 对象，并开始定义样式，用于将地图数据绘制到地图上::

        layer = mapnik.Layer("Layer")
        layer.datasource = source

        style = mapnik.Style()

    接下来，我们设置一个仅适用于高亮要素的规则::

        rule = mapnik.Rule()
        if hiliteExpr != None:
            rule.filter = mapnik.Filter(hiliteExpr)

    此规则将使用“高亮”线条和填充颜色::

        rule.symbols.append(mapnik.PolygonSymbolizer(mapnik.Color(hiliteFill)))
        rule.symbols.append(mapnik.LineSymbolizer(mapnik.Stroke(mapnik.Color(hiliteLine), 0.1)))

    然后，我们将这个规则添加到样式中，并创建另一个规则，仅适用于非高亮要素::

        style.rules.append(rule)

        rule = mapnik.Rule()
        rule.set_else(True)

    此规则将使用“正常”线条和填充颜色::

        rule.symbols.append(mapnik.PolygonSymbolizer(mapnik.Color(normalFill)))
        rule.symbols.append(mapnik.LineSymbolizer(mapnik.Stroke(mapnik.Color(normalLine), 0.1)))

    接着，我们将该规则添加到样式中，并将样式添加到地图和图层中::

        style.rules.append(rule)

        map.append_style("Map Style", style)
        layer.styles.append("Map Style")

    最后，将图层添加到地图中::

        map.layers.append(layer)

    这样， `generateMap()` 函数就通过这些步骤创建了一个完整的地图，定义了数据源、样式和图层，并将其渲染为图像。

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

    `generateMap()` 函数的一个特点是，它可以接收一个点列表，并将这些点直接显示到地图上，而无需将这些点存储到数据库中。这个功能是通过使用 `MemoryDataSource` 数据源和 `ShieldSymbolizer` 来绘制点到地图上实现的::

        if points != None:
            memoryDatasource = mapnik.MemoryDatasource()
            context = mapnik.Context()
            context.push("name")
            next_id = 1
            for long, lat, name in points:
                wkt = "POINT (%0.8f %0.8f)" % (long, lat)
                feature = mapnik.Feature(context, next_id)
                feature['name'] = name
                feature.add_geometries_from_wkt(wkt)
                next_id = next_id + 1
                memoryDatasource.add_feature(feature)

                layer = mapnik.Layer("Points")
                layer.datasource = memoryDatasource

                style = mapnik.Style()
                rule = mapnik.Rule()

                pointImgFile = os.path.join(os.path.dirname(__file__), "point.png")

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

        请注意， `point.png` 文件的路径是通过计算绝对路径来确定的，基于 `mapGenerator.py` 模块本身的位置（通过 `__file__` 全局变量）。这样做是因为该模块可以作为 CGI 脚本的一部分调用，而 CGI 脚本没有当前工作目录。

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

    因为 `mapGenerator.py` 模块旨在与 CGI 脚本一起使用，模块利用一个临时的地图缓存来存储生成的图像文件。在渲染地图图像之前， `generateMap()` 函数需要先创建地图缓存（如果它尚不存在），并在缓存目录中创建一个临时文件来存储生成的地图::

        scriptDir = os.path.dirname(__file__)
        cacheDir = os.path.join(scriptDir, "..", "mapCache")
        if not os.path.exists(cacheDir):
            os.mkdir(cacheDir)
        fd, filename = tempfile.mkstemp(".png", dir=cacheDir)
        os.close(fd)

    最后，我们准备将地图渲染成图像文件，并返回生成的地图文件的相对路径给调用者::

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

    虽然在许多方面， *mapGenerator.py* 模块相当简化，并且专门为满足上一章中介绍的 DISTAL 应用的需求而设计，但深入研究这个模块是值得的，因为它展示了如何利用 *封装* 原则隐藏 Mapnik 的复杂性，从而简化地图生成的过程。使用 *generateMap()* 函数比每次生成地图时都要创建所有数据源、图层、符号化器、规则和样式要容易得多。

    设计一个更通用的地图生成器来处理各种数据源和地图图层，以及返回结果的不同方式，将是一个相对简单的任务，而且不必每次手动定义每个对象。设计并实现这样的模块将非常有价值，特别是如果你希望在 Python 程序中广泛使用 Mapnik。希望这一部分能够给你一些灵感，帮助你着手实现自己的高级 Mapnik 封装模块。

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

