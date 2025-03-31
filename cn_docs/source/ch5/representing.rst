表示和存储地理空间数据
============================================

Representing and storing geospatial data

.. tab:: 中文

    虽然地理空间数据通常以矢量格式文件（如 shapefile）提供，但在某些情况下，shapefile 并不适合或效率较低。一个这样的情况是，当你需要将地理空间数据从一个库中提取，并在另一个库中使用时。例如，假设你已经从一个 shapefile 中读取了一组几何图形，并希望将它们存储到数据库中，或者使用 shapely 库来处理它们。由于不同的 Python 库使用各自的私有类来表示地理空间数据，你不能简单地将一个 OGR Geometry 对象传递给 shapely，或者使用 GDAL SpatialReference 对象来定义数据存储在数据库中所使用的基准面和投影。

    在这种情况下，你需要有一种独立的格式来表示和存储地理空间数据，而这种格式并不限于某一个特定的 Python 库。这种格式，作为矢量格式地理空间数据的通用语言，称为 **Well-Known Text (WKT)** 。

    WKT 是地理空间对象（如点、线或多边形）的紧凑文本描述。例如，以下是定义梵蒂冈边界的几何图形，来自世界边界数据集，并转换为 WKT 字符串：

    .. code-block:: text

        POLYGON ((12.445090330888604 41.90311752178485,
        12.451653339580503 41.907989033391232,
        12.456660170953796 41.901426024699163,
        12.445090330888604 41.90311752178485))

    如你所见，WKT 字符串包含了几何图形的直接文本描述——在本例中，是由四个 x 和 y 坐标构成的多边形。显然，WKT 文本字符串可以比这更复杂，包含成千上万的点，存储多重多边形以及不同几何图形的集合。然而，无论几何图形多么复杂，它仍然可以表示为一个简单的文本字符串。

    .. note::

        还有一种等效的二进制格式，称为 **Well-Known Binary (WKB)**，它以二进制数据存储相同的信息。WKB 经常用于在数据库中存储地理空间数据。

    WKT 字符串还可以用于表示一个 **空间参考**，该空间参考包括投影、基准面和/或坐标系统。例如，以下是一个表示使用 WGS84 基准面的地理坐标系统的 osr.SpatialReference 对象，转换为 WKT 字符串：

    .. code-block:: text

        GEOGCS["WGS 84",DATUM["WGS_1984",SPHEROID["WGS
        84",6378137,298.257223563,AUTHORITY["EPSG","7030"]],TOWGS84[0,0,0,0,
        0,0,0],AUTHORITY["EPSG","6326"]],PRIMEM["Greenwich",0,AUTHORITY["EP
        SG","8901"]],UNIT["degree",0.0174532925199433,AUTHORITY["EPSG","9108"]
        ],AUTHORITY["EPSG","4326"]]

    与几何图形表示一样，WKT 格式的空间参考可以用于将空间参考从一个 Python 库传递到另一个库。

.. tab:: 英文

    While geospatial data is often supplied in the form of vector-format files such as shapefiles, there are situations where shapefiles are unsuitable or inefficient. One such situation is where you need to take geospatial data from one library and use it in a different library. For example, imagine that you have read a set of geometries out of a shapefile and want to store them in a database, or work with them using the shapely library. Because all the different Python libraries use their own private classes to represent geospatial data, you can't just take an OGR Geometry object and pass it to shapely, or use a GDAL SpatialReference object to define the datum and projection to use for data stored in a database.

    In these situations, you need to have an independent format for representing and storing geospatial data that isn't limited to just one particular Python library. This format, the lingua franca for vector-format geospatial data, is called **Well-Known Text (WKT)**.

    WKT is a compact text-based description of a geospatial object such as a point, a line or a polygon. For example, here is the geometry defining the boundary of the Vatican City in the World Borders Dataset, converted into a WKT string:

    .. code-block:: text

        POLYGON ((12.445090330888604 41.90311752178485,
        12.451653339580503 41.907989033391232,
        12.456660170953796 41.901426024699163,
        12.445090330888604 41.90311752178485))

    As you can see, the WKT string contain a straightforward textual description of a geometry—in this case, a polygon consisting of four x and y coordinates. Obviously, WKT text strings can be far more complex than this, containing many thousands of points and storing multipolygons and collections of different geometries. No matter how complex the geometry is, however, it can still be represented as a simple text string.

    .. note::

        There is an equivalent binary format called **Well-Known Binary (WKB)**, which stores the same information as binary data. WKB is often used to store geospatial data within a database.

    WKT strings can also be used to represent a **spatial reference** encompassing a projection, a datum and/or a coordinate system. For example, here is an osr. SpatialReference object representing a geographic coordinate system using the WGS84 datum, converted into a WKT string:

    .. code-block:: text

        GEOGCS["WGS 84",DATUM["WGS_1984",SPHEROID["WGS
        84",6378137,298.257223563,AUTHORITY["EPSG","7030"]],TOWGS84[0,0,0,0,
        0,0,0],AUTHORITY["EPSG","6326"]],PRIMEM["Greenwich",0,AUTHORITY["EP
        SG","8901"]],UNIT["degree",0.0174532925199433,AUTHORITY["EPSG","9108"]
        ],AUTHORITY["EPSG","4326"]]

    As with geometry representations, spatial references in WKT format can be used to pass a spatial reference from one Python library to another.

任务 – 定义泰国和缅甸之间的边界
------------------------------------------------------
Task – define the border between Thailand and Myanmar

.. tab:: 中文

    在这个例子中，我们将使用世界边界数据集来获取定义泰国和缅甸边界的多边形。然后，我们将这些多边形传输到 Shapely，并利用 Shapely 的功能计算这两个国家的共同边界。

    如果你还没有下载，首先从 Thematic Mapping 网站下载世界边界数据集：

    http://thematicmapping.org/downloads/world_borders.php

    世界边界数据集方便地为每个特征提供了 ISO 3166 两字符国家代码，因此在读取 shapefile 时，我们可以识别出与泰国和缅甸对应的特征：

    .. code-block:: python

        from osgeo import ogr

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            if feature.GetField("ISO2") == "TH":
                ...
            elif feature.GetField("ISO2") == "MM":
                ...

    和往常一样，这段代码假设你将 *TM_WORLD_BORDERS-0.3.shp* shapefile 放在与 Python 脚本相同的目录下。如果你将其放置在不同的目录中，你需要调整 *ogr.Open()* 语句以匹配。

    一旦我们识别出我们需要的特征，就可以轻松地将这些特征的几何图形提取为 WKT 字符串：

    .. code-block:: python

        geometry = feature.GetGeometryRef()
        wkt = geometry.ExportToWkt()

    接下来，我们可以使用 shapely.wkt 模块将这些 WKT 字符串转换为 Shapely 几何图形对象：

    .. code-block:: python

        import shapely.wkt
        ...
        border = shapely.wkt.loads(wkt)

    现在我们已经将国家轮廓转到 Shapely 中，便可以使用 Shapely 的计算几何能力来计算这两个国家的共同边界：

    .. code-block:: python

        commonBorder = thailandBorder.intersection(myanmarBorder)

    结果将是一个 LineString（如果边界被分割为多个部分，则为 MultiLineString）。如果我们愿意，我们可以将这个 Shapely 对象再次转换回 OGR 几何图形，并再次保存为 shapefile：

    .. code-block:: python

        wkt = shapely.wkt.dumps(commonBorder)
        feature = ogr.Feature(dstLayer.GetLayerDefn())
        feature.SetGeometry(ogr.CreateGeometryFromWkt(wkt))
        dstLayer.CreateFeature(feature)
        feature.Destroy()

    将共同边界保存到 shapefile 中后，我们最终可以将结果显示为地图：

    .. image:: ./img/155-0.png
        :scale: 50
        :class: with-border
        :align: center

    `common-border/border.shp` shapefile 的内容由沿着两个国家共同边界的粗线表示。

    以下是用于计算此共同边界的完整程序：

    .. code-block:: python

        # calcCommonBorders.py

        import os, os.path, shutil
        from osgeo import ogr
        import shapely.wkt

        # 从世界边界数据集加载泰国和缅甸的多边形。

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        thailand = None
        myanmar = None

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            if feature.GetField("ISO2") == "TH":
                geometry = feature.GetGeometryRef()
                thailand = shapely.wkt.loads(geometry.ExportToWkt())
            elif feature.GetField("ISO2") == "MM":
                geometry = feature.GetGeometryRef()
                myanmar = shapely.wkt.loads(geometry.ExportToWkt())

        # 计算共同边界。

        commonBorder = thailand.intersection(myanmar)

        # 将共同边界保存到新的 shapefile 中。

        if os.path.exists("common-border"):
            shutil.rmtree("common-border")
        os.mkdir("common-border")

        spatialReference = osr.SpatialReference()
        spatialReference.SetWellKnownGeogCS('WGS84')

        driver = ogr.GetDriverByName("ESRI Shapefile")
        dstPath = os.path.join("common-border", "border.shp")
        dstFile = driver.CreateDataSource(dstPath)
        dstLayer = dstFile.CreateLayer("layer", spatialReference)

        wkt = shapely.wkt.dumps(commonBorder)

        feature = ogr.Feature(dstLayer.GetLayerDefn())
        feature.SetGeometry(ogr.CreateGeometryFromWkt(wkt))
        dstLayer.CreateFeature(feature)
        feature.Destroy()
        
        dstFile.Destroy()

    如果你将 *TM_WORLD_BORDERS-0.3.shp* shapefile 放置在不同的目录中，修改 *ogr.Open()* 语句以包含正确的目录路径。

    我们将在本章后续部分使用这个 shapefile 来计算泰国-缅甸边界的长度，因此请确保生成并保留 *common-borders/border.shp* shapefile 的副本。

.. tab:: 英文

    In this recipe, we will make use of the World Borders Dataset to obtain polygons defining the borders of Thailand and Myanmar. We will then transfer these polygons into Shapely, and use Shapely's capabilities to calculate the common border between these two countries.

    If you haven't already done so, download the World Borders Dataset from the Thematic Mapping website:

    http://thematicmapping.org/downloads/world_borders.php

    The World Borders Dataset conveniently includes ISO 3166 two-character country codes for each feature, so we can identify the features corresponding to Thailand and Myanmar as we read through the shapefile:

    .. code-block:: python

        from osgeo import ogr

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            if feature.GetField("ISO2") == "TH":
                ...
            elif feature.GetField("ISO2") == "MM":
                ...

    As usual, this code assumes that you have placed the *TM_WORLD_BORDERS-0.3.shp* shapefile in the same directory as the Python script. If you've placed it into a different directory, you'll need to adjust the *ogr.Open()* statement to match.

    Once we have identified the features we want, it is easy to extract the features' geometries as WKT strings:

    .. code-block:: python

        geometry = feature.GetGeometryRef()
        wkt = geometry.ExportToWkt()

    We can then convert these to Shapely geometry objects using the shapely.wkt module:

    .. code-block:: python

        import shapely.wkt
        ...
        border = shapely.wkt.loads(wkt)

    Now that we have the country outlines in Shapely, we can use Shapely's computational geometry capabilities to calculate the common border between these two countries:

    .. code-block:: python

        commonBorder = thailandBorder.intersection(myanmarBorder)

    The result will be a LineString (or a MultiLineString if the border is broken up into more than one part). If we wanted to, we could then convert this Shapely object back into a OGR geometry, and save it into a shapefile again:

    .. code-block:: python

        wkt = shapely.wkt.dumps(commonBorder)
        feature = ogr.Feature(dstLayer.GetLayerDefn())
        feature.SetGeometry(ogr.CreateGeometryFromWkt(wkt))
        dstLayer.CreateFeature(feature)
        feature.Destroy()

    With the common border saved into a shapefile, we can finally display the results as a map:

    .. image:: ./img/155-0.png
       :scale: 50
       :class: with-border
       :align: center
    
    The contents of the common-border/border.shp shapefile is represented by the heavy line along the countries' common border.

    Here is the entire program used to calculate this common border:

    .. code-block:: python

        # calcCommonBorders.py
    
        import os,os.path,shutil
        from osgeo import ogr
        import shapely.wkt

        # Load the thai and myanmar polygons from the world borders
        # dataset.

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        thailand = None
        myanmar = None

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            if feature.GetField("ISO2") == "TH":
                geometry = feature.GetGeometryRef()
                thailand = shapely.wkt.loads(geometry.ExportToWkt())
            elif feature.GetField("ISO2") == "MM":
                geometry = feature.GetGeometryRef()
                myanmar = shapely.wkt.loads(geometry.ExportToWkt())

        # Calculate the common border.

        commonBorder = thailand.intersection(myanmar)

        # Save the common border into a new shapefile.

        if os.path.exists("common-border"):
            shutil.rmtree("common-border")
        os.mkdir("common-border")

        spatialReference = osr.SpatialReference()
        spatialReference.SetWellKnownGeogCS('WGS84')

        driver = ogr.GetDriverByName("ESRI Shapefile")
        dstPath = os.path.join("common-border", "border.shp")
        dstFile = driver.CreateDataSource(dstPath)
        dstLayer = dstFile.CreateLayer("layer", spatialReference)

        wkt = shapely.wkt.dumps(commonBorder)

        feature = ogr.Feature(dstLayer.GetLayerDefn())
        feature.SetGeometry(ogr.CreateGeometryFromWkt(wkt))
        dstLayer.CreateFeature(feature)
        feature.Destroy()
        
        dstFile.Destroy()

    If you've placed your *TM_WORLD_BORDERS-0.3.shp* shapefile into a different directory, change the *ogr.Open()* statement to include the correct directory path.

    We will use this shapefile later in this chapter to calculate the length of the Thailand – Myanmar border, so make sure you generate and keep a copy of the *common-borders/border.shp* shapefile.



任务 – 将几何图形保存到文本文件中
------------------------------------------------------
Task – save geometries into a text file

.. tab:: 中文

    WKT 不仅仅用于在 Python 库之间传递几何图形，它也可以作为存储地理空间数据的一种有用方式，而无需处理使用 shapefile 时所带来的复杂性和限制。

    在这个例子中，我们将从世界边界数据集中读取一组多边形，将它们转换为 WKT 格式，并将其保存为文本文件：

    .. code-block:: python

        # saveAsText.py

        import os, os.path, shutil
        from osgeo import ogr

        if os.path.exists("country-wkt-files"):
            shutil.rmtree("country-wkt-files")
        os.mkdir("country-wkt-files")

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME")
            geometry = feature.GetGeometryRef()

            f = file(os.path.join("country-wkt-files",
                                    name + ".txt"), "w")
            f.write(geometry.ExportToWkt())
            f.close()

    和往常一样，如果你将 shapefile 存储在其他目录中，需要更改 *ogr.Open()* 语句以包含正确的目录路径。

    你可能会想知道为什么要这么做，而不是创建一个 shapefile 来存储你的地理空间数据。事实上，shapefiles 是有限制的，因为同一个 shapefile 中的所有特征必须具有相同的几何类型。此外，设置元数据和保存几何图形的复杂性对于某些应用来说可能是多余的。有时候，处理纯文本会更简单。

.. tab:: 英文

    WKT is not only useful for transferring geometries from one Python library to another. It can also be a useful way of storing geospatial data without having to deal with the complexity and constraints imposed by using shapefiles.

    In this example, we will read a set of polygons from the World Borders Dataset, convert them to WKT format, and save them as text files:

    .. code-block:: python

        # saveAsText.py

        import os,os.path,shutil
        from osgeo import ogr

        if os.path.exists("country-wkt-files"):
            shutil.rmtree("country-wkt-files")
        os.mkdir("country-wkt-files")

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME")
            geometry = feature.GetGeometryRef()

            f = file(os.path.join("country-wkt-files",
                                  name + ".txt"), "w")
            f.write(geometry.ExportToWkt())
            f.close()

    As usual, you'll need to change the *ogr.Open()* statement to include a directory path if you've stored the shapefile in a different directory.

    You might be wondering why you want to do this, rather than creating a shapefile to store your geospatial data. Well, shapefiles are limited, in that all the features in a single shapefile must have the same geometry type. Also, the complexity of setting up metadata and saving geometries can be overkill for some applications. Sometimes, dealing with plain text is just easier.


