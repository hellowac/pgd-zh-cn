表示和存储地理空间数据
============================================

Representing and storing geospatial data

.. tab:: 中文

    While geospatial data is often supplied in the form of vector-format files such as shapefiles, there are situations where shapefiles are unsuitable or inefficient. One such situation is where you need to take geospatial data from one library and use it in a different library. For example, imagine that you have read a set of geometries out of a shapefile and want to store them in a database, or work with them using the shapely library. Because all the different Python libraries use their own private classes to represent geospatial data, you can't just take an OGR Geometry object and pass it to shapely, or use a GDAL SpatialReference object to define the datum and projection to use for data stored in a database.

    In these situations, you need to have an independent format for representing and storing geospatial data that isn't limited to just one particular Python library. This format, the lingua franca for vector-format geospatial data, is called **Well-Known Text (WKT)**.

    WKT is a compact text-based description of a geospatial object such as a point, a line or a polygon. For example, here is the geometry defining the boundary of the Vatican City in the World Borders Dataset, converted into a WKT string:

    .. code-block:: txt

        POLYGON ((12.445090330888604 41.90311752178485,
        12.451653339580503 41.907989033391232,
        12.456660170953796 41.901426024699163,
        12.445090330888604 41.90311752178485))

    As you can see, the WKT string contain a straightforward textual description of a geometry—in this case, a polygon consisting of four x and y coordinates. Obviously, WKT text strings can be far more complex than this, containing many thousands of points and storing multipolygons and collections of different geometries. No matter how complex the geometry is, however, it can still be represented as a simple text string.

    .. note::

        There is an equivalent binary format called **Well-Known Binary (WKB)**, which stores the same information as binary data. WKB is often used to store geospatial data within a database.

    WKT strings can also be used to represent a **spatial reference** encompassing a projection, a datum and/or a coordinate system. For example, here is an osr. SpatialReference object representing a geographic coordinate system using the WGS84 datum, converted into a WKT string:

    .. code-block:: txt

        GEOGCS["WGS 84",DATUM["WGS_1984",SPHEROID["WGS
        84",6378137,298.257223563,AUTHORITY["EPSG","7030"]],TOWGS84[0,0,0,0,
        0,0,0],AUTHORITY["EPSG","6326"]],PRIMEM["Greenwich",0,AUTHORITY["EP
        SG","8901"]],UNIT["degree",0.0174532925199433,AUTHORITY["EPSG","9108"]
        ],AUTHORITY["EPSG","4326"]]

    As with geometry representations, spatial references in WKT format can be used to pass a spatial reference from one Python library to another.

.. tab:: 英文

    While geospatial data is often supplied in the form of vector-format files such as shapefiles, there are situations where shapefiles are unsuitable or inefficient. One such situation is where you need to take geospatial data from one library and use it in a different library. For example, imagine that you have read a set of geometries out of a shapefile and want to store them in a database, or work with them using the shapely library. Because all the different Python libraries use their own private classes to represent geospatial data, you can't just take an OGR Geometry object and pass it to shapely, or use a GDAL SpatialReference object to define the datum and projection to use for data stored in a database.

    In these situations, you need to have an independent format for representing and storing geospatial data that isn't limited to just one particular Python library. This format, the lingua franca for vector-format geospatial data, is called **Well-Known Text (WKT)**.

    WKT is a compact text-based description of a geospatial object such as a point, a line or a polygon. For example, here is the geometry defining the boundary of the Vatican City in the World Borders Dataset, converted into a WKT string:

    .. code-block:: txt

        POLYGON ((12.445090330888604 41.90311752178485,
        12.451653339580503 41.907989033391232,
        12.456660170953796 41.901426024699163,
        12.445090330888604 41.90311752178485))

    As you can see, the WKT string contain a straightforward textual description of a geometry—in this case, a polygon consisting of four x and y coordinates. Obviously, WKT text strings can be far more complex than this, containing many thousands of points and storing multipolygons and collections of different geometries. No matter how complex the geometry is, however, it can still be represented as a simple text string.

    .. note::

        There is an equivalent binary format called **Well-Known Binary (WKB)**, which stores the same information as binary data. WKB is often used to store geospatial data within a database.

    WKT strings can also be used to represent a **spatial reference** encompassing a projection, a datum and/or a coordinate system. For example, here is an osr. SpatialReference object representing a geographic coordinate system using the WGS84 datum, converted into a WKT string:

    .. code-block:: txt

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


