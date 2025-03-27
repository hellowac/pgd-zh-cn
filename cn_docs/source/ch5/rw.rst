读取和写入地理空间数据
============================================

Reading and writing geospatial data

.. tab:: 中文

    In this section, we will look at some examples of tasks you might want to perform
    which involve reading and writing geospatial data in both vector and raster format.

.. tab:: 英文

    In this section, we will look at some examples of tasks you might want to perform
    which involve reading and writing geospatial data in both vector and raster format.



任务 – 计算世界上每个国家的边界​​框
-------------------------------------------------------------------
Task – calculate the bounding box for each country in the world

.. tab:: 中文

    In this slightly contrived example, we will make use of a shapefile to calculate the
    minimum and maximum latitude/longitude values for each country in the world.
    This "bounding box" can be used, among other things, to generate a map of a
    particular country. For example, the bounding box for Turkey would look like this:

    .. image:: ./img/129-0.png
       :scale: 70
       :align: center
       :class: with-border

    Start by downloading the World Borders Dataset from:

    http://thematicmapping.org/downloads/world_borders.php

    Decompress the .zip archive and place the various files that make up the shapefile (the .dbf, .prj, .shp, and .shx files) together in a suitable directory.

    Next, we need to create a Python program which can read the borders for each country.
    Fortunately, using OGR to read through the contents of a shapefile is trivial:
    
    .. code-block:: python

        from osgeo import ogr

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
    
    The feature consists of a **geometry** and a set of **fields**. For this data, the geometry is a
    polygon that defines the outline of the country, while the fields contain various pieces
    of information about the country. According to the *Readme.txt* file, the fields in this
    shapefile include the ISO-3166 three-letter code for the country (in a field named ISO3)
    as well as the name for the country (in a field named NAME). This allows us to obtain the
    country code and name like this:
    
    .. code-block:: python

        countryCode = feature.GetField("ISO3")
        countryName = feature.GetField("NAME")

    We can also obtain the country's border polygon using:
    
    .. code-block:: python

        geometry = feature.GetGeometryRef()

    There are all sorts of things we can do with this geometry. For example, we could
    calculate the geometry's centroid, test if a point lies within the polygon, or convert
    the polygon to WKT format. In this case, however, we want to obtain the bounding
    box or **envelope** for the polygon. We can do this in the following way:
    
    .. code-block:: python

        minLong,maxLong,minLat,maxLat = geometry.GetEnvelope()

    Let's put all this together into a complete working program:
    
    .. code-block:: python

        # calcBoundingBoxes.py

        from osgeo import ogr

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        countries = [] # List of (code,name,minLat,maxLat,
                       # minLong,maxLong) tuples.

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            countryCode = feature.GetField("ISO3")
            countryName = feature.GetField("NAME")
            geometry = feature.GetGeometryRef()
            minLong,maxLong,minLat,maxLat = geometry.GetEnvelope()

            countries.append((countryName, countryCode,
                              minLat, maxLat, minLong, maxLong))
        
        countries.sort()
        
        for name,code,minLat,maxLat,minLong,maxLong in countries:
            print "%s (%s) lat=%0.4f..%0.4f, long=%0.4f..%0.4f" \
            % (name, code,minLat, maxLat,minLong, maxLong)

    .. note::

        If you aren't storing the TM_WORLD_BORDERS-0.3.shp shapefile in the same directory as the script itself, you will need to add the directory where the shapefile is stored to your ogr.Open() call. You can also store the boundingBoxes.shp shapefile in a different directory if you prefer, by changing the path where this shapefile is created.

    Running this program produces the following output:
    
    .. code-block:: txt

        % python calcBoundingBoxes.py
        Afghanistan (AFG) lat=29.4061..38.4721, long=60.5042..74.9157
        Albania (ALB) lat=39.6447..42.6619, long=19.2825..21.0542
        Algeria (DZA) lat=18.9764..37.0914, long=-8.6672..11.9865

.. tab:: 英文

    In this slightly contrived example, we will make use of a shapefile to calculate the
    minimum and maximum latitude/longitude values for each country in the world.
    This "bounding box" can be used, among other things, to generate a map of a
    particular country. For example, the bounding box for Turkey would look like this:

    .. image:: ./img/129-0.png
       :scale: 70
       :align: center
       :class: with-border

    Start by downloading the World Borders Dataset from:

    http://thematicmapping.org/downloads/world_borders.php

    Decompress the .zip archive and place the various files that make up the shapefile (the .dbf, .prj, .shp, and .shx files) together in a suitable directory.

    Next, we need to create a Python program which can read the borders for each country.
    Fortunately, using OGR to read through the contents of a shapefile is trivial:
    
    .. code-block:: python

        from osgeo import ogr

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
    
    The feature consists of a **geometry** and a set of **fields**. For this data, the geometry is a
    polygon that defines the outline of the country, while the fields contain various pieces
    of information about the country. According to the *Readme.txt* file, the fields in this
    shapefile include the ISO-3166 three-letter code for the country (in a field named ISO3)
    as well as the name for the country (in a field named NAME). This allows us to obtain the
    country code and name like this:
    
    .. code-block:: python

        countryCode = feature.GetField("ISO3")
        countryName = feature.GetField("NAME")

    We can also obtain the country's border polygon using:
    
    .. code-block:: python

        geometry = feature.GetGeometryRef()

    There are all sorts of things we can do with this geometry. For example, we could
    calculate the geometry's centroid, test if a point lies within the polygon, or convert
    the polygon to WKT format. In this case, however, we want to obtain the bounding
    box or **envelope** for the polygon. We can do this in the following way:
    
    .. code-block:: python

        minLong,maxLong,minLat,maxLat = geometry.GetEnvelope()

    Let's put all this together into a complete working program:
    
    .. code-block:: python

        # calcBoundingBoxes.py

        from osgeo import ogr

        shapefile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
        layer = shapefile.GetLayer(0)

        countries = [] # List of (code,name,minLat,maxLat,
                       # minLong,maxLong) tuples.

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            countryCode = feature.GetField("ISO3")
            countryName = feature.GetField("NAME")
            geometry = feature.GetGeometryRef()
            minLong,maxLong,minLat,maxLat = geometry.GetEnvelope()

            countries.append((countryName, countryCode,
                              minLat, maxLat, minLong, maxLong))
        
        countries.sort()
        
        for name,code,minLat,maxLat,minLong,maxLong in countries:
            print "%s (%s) lat=%0.4f..%0.4f, long=%0.4f..%0.4f" \
            % (name, code,minLat, maxLat,minLong, maxLong)

    .. note::

        If you aren't storing the TM_WORLD_BORDERS-0.3.shp shapefile in the same directory as the script itself, you will need to add the directory where the shapefile is stored to your ogr.Open() call. You can also store the boundingBoxes.shp shapefile in a different directory if you prefer, by changing the path where this shapefile is created.

    Running this program produces the following output:
    
    .. code-block:: txt

        % python calcBoundingBoxes.py
        Afghanistan (AFG) lat=29.4061..38.4721, long=60.5042..74.9157
        Albania (ALB) lat=39.6447..42.6619, long=19.2825..21.0542
        Algeria (DZA) lat=18.9764..37.0914, long=-8.6672..11.9865


任务 – 将国家边界框保存到 Shapefile 中
-------------------------------------------------------------------
Task – save the country bounding boxes into a shapefile

.. tab:: 中文

    While the previous example simply printed out the latitude and longitude values, it
    might be more useful to draw the bounding boxes onto a map. To do this, we have to
    convert the bounding boxes into polygons, and save these polygons into a shapefile.
    
    Creating a shapefile involves the following steps:

    1. Define the **spatial reference** used by the shapefile's data. In this case, we'll use the WGS84 datum and unprojected geographic coordinates (that is latitude and longitude values). You can define this spatial reference using OGR in the following way:
    
       .. code-block:: python

          from osgeo import osr

          spatialReference = osr.SpatialReference()
          spatialReference.SetWellKnownGeogCS('WGS84')

    2. We can now create the shapefile itself using this spatial reference:
    
       .. code-block:: python

          from osgeo import ogr
          driver = ogr.GetDriverByName("ESRI Shapefile")
          dstFile = driver.CreateDataSource("boundingBoxes.shp"))
          dstLayer = dstFile.CreateLayer("layer", spatialReference)

    3. After creating the shapefile, you next define the various fields which will hold the metadata for each feature. In this case, let's add two fields, to store the country name and ISO-3166 code for each country:
    
       .. code-block:: python

          fieldDef = ogr.FieldDefn("COUNTRY", ogr.OFTString)
          fieldDef.SetWidth(50)
          dstLayer.CreateField(fieldDef)

          fieldDef = ogr.FieldDefn("CODE", ogr.OFTString)
          fieldDef.SetWidth(3)
          dstLayer.CreateField(fieldDef)

    4. We now need to create the geometry for each feature—in this case, a polygon defining the country's bounding box. A polygon consists of one or more linear **rings**; the first linear ring defines the exterior of the polygon, while additional rings define "holes" inside the polygon. In this case, we want a simple polygon with a rectangular exterior and no holes:
    
       .. code-block:: python

          linearRing = ogr.Geometry(ogr.wkbLinearRing)
          linearRing.AddPoint(minLong, minLat)
          linearRing.AddPoint(maxLong, minLat)
          linearRing.AddPoint(maxLong, maxLat)
          linearRing.AddPoint(minLong, maxLat)
          linearRing.AddPoint(minLong, minLat)

          polygon = ogr.Geometry(ogr.wkbPolygon)
          polygon.AddGeometry(linearRing)

       .. note::
          
          You may have noticed that the coordinate (minLong, minLat) was added to the linear ring twice. This is because we are defining line segments rather than just points—the first call to AddPoint() defines the starting point, and each subsequent call to AddPoint() adds a new line segment to the linear ring. In this case, we start in the lower-left corner and move counter-clockwise around the bounding box until we reach the lower-left corner again:

          .. image:: ./img/133-0.png
             :scale: 80
             :class: with-border
             :align: center

    5. Once we have the polygon, we can use it to create a feature:
    
       .. code-block:: python

          feature = ogr.Feature(dstLayer.GetLayerDefn())
          feature.SetGeometry(polygon)
          feature.SetField("COUNTRY", countryName)
          feature.SetField("CODE", countryCode)
          dstLayer.CreateFeature(feature)
          feature.Destroy()

       Notice how we use the setField() method to store the feature's metadata.
       We also have to call the Destroy() method to close the feature once we have
       finished with it; this ensures that the feature is saved into the shapefile.

    6. Finally, we call the output shapefile's Destroy() method to close the file:
    
       .. code-block:: python

          dstFile.Destroy()

       Putting all this together, and combining it with the code from the previous recipe to calculate the bounding boxes for each country in the World Borders Dataset shapefile, we end up with the following complete program:
    
       .. code-block:: python

          # boundingBoxesToShapefile.py

          import os, os.path, shutil

          from osgeo import ogr
          from osgeo import osr

          # Open the source shapefile.

          srcFile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
          srcLayer = srcFile.GetLayer(0)

          # Open the output shapefile.

          if os.path.exists("bounding-boxes"):
              shutil.rmtree("bounding-boxes")
          os.mkdir("bounding-boxes")

          spatialReference = osr.SpatialReference()
          spatialReference.SetWellKnownGeogCS('WGS84')

          driver = ogr.GetDriverByName("ESRI Shapefile")
          dstPath = os.path.join("bounding-boxes", "boundingBoxes.shp")
          dstFile = driver.CreateDataSource(dstPath)
          dstLayer = dstFile.CreateLayer("layer", spatialReference)

          fieldDef = ogr.FieldDefn("COUNTRY", ogr.OFTString)
          fieldDef.SetWidth(50)
          dstLayer.CreateField(fieldDef)

          fieldDef = ogr.FieldDefn("CODE", ogr.OFTString)
          fieldDef.SetWidth(3)
          dstLayer.CreateField(fieldDef)

          # Read the country features from the source shapefile.

          for i in range(srcLayer.GetFeatureCount()):
              feature = srcLayer.GetFeature(i)
              countryCode = feature.GetField("ISO3")
              countryName = feature.GetField("NAME")
              geometry = feature.GetGeometryRef()
              minLong,maxLong,minLat,maxLat = geometry.GetEnvelope()

              # Save the bounding box as a feature in the output
              # shapefile.

              linearRing = ogr.Geometry(ogr.wkbLinearRing)
              linearRing.AddPoint(minLong, minLat)
              linearRing.AddPoint(maxLong, minLat)
              linearRing.AddPoint(maxLong, maxLat)
              linearRing.AddPoint(minLong, maxLat)
              linearRing.AddPoint(minLong, minLat)
              polygon = ogr.Geometry(ogr.wkbPolygon)
              polygon.AddGeometry(linearRing)
              feature = ogr.Feature(dstLayer.GetLayerDefn())
              feature.SetGeometry(polygon)
              feature.SetField("COUNTRY", countryName)
              feature.SetField("CODE", countryCode)
              dstLayer.CreateFeature(feature)
              feature.Destroy()
  
          # All done.
            
          srcFile.Destroy()
          dstFile.Destroy()

    The only unexpected twist in this program is the use of a subdirectory called
    *bounding-boxes* that is used to store the output shapefile. Because a shapefile is
    actually made up of multiple files on disk (a *.dbf* file, a *.prj* file, a *.shp* file, and
    a *.shx* file), it is easier to place these together in a subdirectory. We use the Python
    Standard Library module *shutil* to delete the previous contents of this directory,
    and then *os.mkdir()* to create it again.

    Running this program creates the bounding box shapefile, which we can then draw
    onto a map. For example, here is the outline of Thailand along with a bounding box
    taken from the *boundingBox.shp* shapefile:

    .. image:: ./img/136-0.png
       :scale: 50
       :class: with-border
       :align: center

    We will be looking at how to draw maps like this in *Chapter 8, Using Python and Mapnik to Generate Maps*.

.. tab:: 英文

    While the previous example simply printed out the latitude and longitude values, it
    might be more useful to draw the bounding boxes onto a map. To do this, we have to
    convert the bounding boxes into polygons, and save these polygons into a shapefile.
    
    Creating a shapefile involves the following steps:

    1. Define the **spatial reference** used by the shapefile's data. In this case, we'll use the WGS84 datum and unprojected geographic coordinates (that is latitude and longitude values). You can define this spatial reference using OGR in the following way:
    
       .. code-block:: python

          from osgeo import osr

          spatialReference = osr.SpatialReference()
          spatialReference.SetWellKnownGeogCS('WGS84')

    2. We can now create the shapefile itself using this spatial reference:
    
       .. code-block:: python

          from osgeo import ogr
          driver = ogr.GetDriverByName("ESRI Shapefile")
          dstFile = driver.CreateDataSource("boundingBoxes.shp"))
          dstLayer = dstFile.CreateLayer("layer", spatialReference)

    3. After creating the shapefile, you next define the various fields which will hold the metadata for each feature. In this case, let's add two fields, to store the country name and ISO-3166 code for each country:
    
       .. code-block:: python

          fieldDef = ogr.FieldDefn("COUNTRY", ogr.OFTString)
          fieldDef.SetWidth(50)
          dstLayer.CreateField(fieldDef)

          fieldDef = ogr.FieldDefn("CODE", ogr.OFTString)
          fieldDef.SetWidth(3)
          dstLayer.CreateField(fieldDef)

    4. We now need to create the geometry for each feature—in this case, a polygon defining the country's bounding box. A polygon consists of one or more linear **rings**; the first linear ring defines the exterior of the polygon, while additional rings define "holes" inside the polygon. In this case, we want a simple polygon with a rectangular exterior and no holes:
    
       .. code-block:: python

          linearRing = ogr.Geometry(ogr.wkbLinearRing)
          linearRing.AddPoint(minLong, minLat)
          linearRing.AddPoint(maxLong, minLat)
          linearRing.AddPoint(maxLong, maxLat)
          linearRing.AddPoint(minLong, maxLat)
          linearRing.AddPoint(minLong, minLat)

          polygon = ogr.Geometry(ogr.wkbPolygon)
          polygon.AddGeometry(linearRing)

       .. note::
          
          You may have noticed that the coordinate (minLong, minLat) was added to the linear ring twice. This is because we are defining line segments rather than just points—the first call to AddPoint() defines the starting point, and each subsequent call to AddPoint() adds a new line segment to the linear ring. In this case, we start in the lower-left corner and move counter-clockwise around the bounding box until we reach the lower-left corner again:

          .. image:: ./img/133-0.png
             :scale: 80
             :class: with-border
             :align: center

    5. Once we have the polygon, we can use it to create a feature:
    
       .. code-block:: python

          feature = ogr.Feature(dstLayer.GetLayerDefn())
          feature.SetGeometry(polygon)
          feature.SetField("COUNTRY", countryName)
          feature.SetField("CODE", countryCode)
          dstLayer.CreateFeature(feature)
          feature.Destroy()

       Notice how we use the setField() method to store the feature's metadata.
       We also have to call the Destroy() method to close the feature once we have
       finished with it; this ensures that the feature is saved into the shapefile.

    6. Finally, we call the output shapefile's Destroy() method to close the file:
    
       .. code-block:: python

          dstFile.Destroy()

       Putting all this together, and combining it with the code from the previous recipe to calculate the bounding boxes for each country in the World Borders Dataset shapefile, we end up with the following complete program:
    
       .. code-block:: python

          # boundingBoxesToShapefile.py

          import os, os.path, shutil

          from osgeo import ogr
          from osgeo import osr

          # Open the source shapefile.

          srcFile = ogr.Open("TM_WORLD_BORDERS-0.3.shp")
          srcLayer = srcFile.GetLayer(0)

          # Open the output shapefile.

          if os.path.exists("bounding-boxes"):
              shutil.rmtree("bounding-boxes")
          os.mkdir("bounding-boxes")

          spatialReference = osr.SpatialReference()
          spatialReference.SetWellKnownGeogCS('WGS84')

          driver = ogr.GetDriverByName("ESRI Shapefile")
          dstPath = os.path.join("bounding-boxes", "boundingBoxes.shp")
          dstFile = driver.CreateDataSource(dstPath)
          dstLayer = dstFile.CreateLayer("layer", spatialReference)

          fieldDef = ogr.FieldDefn("COUNTRY", ogr.OFTString)
          fieldDef.SetWidth(50)
          dstLayer.CreateField(fieldDef)

          fieldDef = ogr.FieldDefn("CODE", ogr.OFTString)
          fieldDef.SetWidth(3)
          dstLayer.CreateField(fieldDef)

          # Read the country features from the source shapefile.

          for i in range(srcLayer.GetFeatureCount()):
              feature = srcLayer.GetFeature(i)
              countryCode = feature.GetField("ISO3")
              countryName = feature.GetField("NAME")
              geometry = feature.GetGeometryRef()
              minLong,maxLong,minLat,maxLat = geometry.GetEnvelope()

              # Save the bounding box as a feature in the output
              # shapefile.

              linearRing = ogr.Geometry(ogr.wkbLinearRing)
              linearRing.AddPoint(minLong, minLat)
              linearRing.AddPoint(maxLong, minLat)
              linearRing.AddPoint(maxLong, maxLat)
              linearRing.AddPoint(minLong, maxLat)
              linearRing.AddPoint(minLong, minLat)
              polygon = ogr.Geometry(ogr.wkbPolygon)
              polygon.AddGeometry(linearRing)
              feature = ogr.Feature(dstLayer.GetLayerDefn())
              feature.SetGeometry(polygon)
              feature.SetField("COUNTRY", countryName)
              feature.SetField("CODE", countryCode)
              dstLayer.CreateFeature(feature)
              feature.Destroy()
  
          # All done.
            
          srcFile.Destroy()
          dstFile.Destroy()

    The only unexpected twist in this program is the use of a subdirectory called
    *bounding-boxes* that is used to store the output shapefile. Because a shapefile is
    actually made up of multiple files on disk (a *.dbf* file, a *.prj* file, a *.shp* file, and
    a *.shx* file), it is easier to place these together in a subdirectory. We use the Python
    Standard Library module *shutil* to delete the previous contents of this directory,
    and then *os.mkdir()* to create it again.

    Running this program creates the bounding box shapefile, which we can then draw
    onto a map. For example, here is the outline of Thailand along with a bounding box
    taken from the *boundingBox.shp* shapefile:

    .. image:: ./img/136-0.png
       :scale: 50
       :class: with-border
       :align: center

    We will be looking at how to draw maps like this in *Chapter 8, Using Python and Mapnik to Generate Maps*.



任务 – 使用数字高程图分析高度数据
-------------------------------------------------------------------
Task – analyze height data using a digital elevation map

.. tab:: 中文

    A **Digital Elevation Map (DEM)** is a raster geospatial data format where each pixel
    value represents the height of a point on the Earth's surface. We encountered DEM
    files in the previous chapter, where we saw two examples of data sources that supply
    this type of information: the National Elevation Dataset covering the United States,
    and GLOBE which provides DEM files covering the entire Earth.

    Because a DEM file contains height data, it can be interesting to analyze the height
    values for a given area. For example, we could draw a histogram showing how
    much of a country's area is at a certain elevation. Let's take some DEM data from
    the GLOBE dataset, and calculate a height histogram using that data.

    To keep things simple, we will choose a small country surrounded by ocean:
    New Zealand.

    .. note::

        We're using a small country so that we don't have too much data to work with, and we're using a country surrounded by ocean so that we can check all the points within a bounding box, rather than having to use a polygon to exclude points outside of the country's boundaries.

    To download the DEM data, go to the GLOBE website (http://www.ngdc.noaa.
    gov/mgg/topo/globe.html) and click on the **Get Data Online** hyperlink. We're
    going to use the data already calculated for this area of the world, so click on the
    **Any or all 16 "tiles"** hyperlink. New Zealand is in tile L, so click on the hyperlink
    for this tile to download it.

    The file you download will be called *l10g.zip* (or *l10g.gz* if you chose to download
    the tile in GZIP format). If you decompress it, you will end up with a single file called
    *l10g* containing the raw elevation data.

    By itself, this file isn't very useful—it needs to be georeferenced onto the Earth's
    surface so that you can match up each height value with its position on the Earth.
    To do this, you need to download the associated header file. Unfortunately, the
    GLOBE website makes this rather difficult; the header files for the premade tiles
    can be found at:

    http://www.ngdc.noaa.gov/mgg/topo/elev/esri/hdr

    Download the file named l10g.hdr and place it into the same directory as the l10g
    file you downloaded earlier. You can then read the DEM file using GDAL:

    .. code-block:: python

       from osgeo import gdal
       dataset = gdal.Open("l10g")

    As you must have noticed when you downloaded the l10g tile that this tile covers
    much more than just New Zealand—all of Australia is included, as well as Malaysia,
    Papua New Guinea, and several other East-Asian countries. To work with the height
    data for just New Zealand, we have to be able to identify the relevant portion of the
    raster DEM—that is, the range of x and y coordinates which cover New Zealand.
    We start by looking at a map and identifying the minimum and maximum latitude/
    longitude values which enclose all of New Zealand, but no other country:

    .. image:: ./img/138-0.png
       :scale: 50
       :class: with-border
       :align: center

    Rounded to the nearest whole degree, we get a longitude/latitude bounding box of
    (165, -48)…(179, -33). This is the area we want to scan to cover all of New Zealand.

    There is, however, a problem: the raster data consists of pixels or "cells" identified
    by x and y coordinates, not longitude and latitude values. We have to convert from
    longitudes and latitudes into x and y coordinates. To do this, we need to make use
    of the raster DEM's **affine transformation**.

    If you remember, back in *Chapter 3, Python Libraries for Geospatial Development*, we
    discussed that an affine transformation is a complex system for mapping geographic
    coordinates (latitude and longitude values) into raster (x, y) coordinates. Fortunately
    we don't have to deal with these formulas directly, as GDAL will do it for us. We
    start by obtaining our dataset's affine transformation:

    .. code-block:: python

       t = dataset.GetGeoTransform()

    Using this transformation, we can convert an (x, y) coordinate into its associated
    latitude and longitude value. In this case, however, we want to do the opposite—we
    want to take a latitude and longitude, and calculate the associated x and y coordinate.

    To do this, we have to invert the affine transformation. Once again, GDAL will do
    this for us:

    .. code-block:: python

       success,tInverse = gdal.InvGeoTransform(t)
       if not success:
           print "Failed!"
           sys.exit(1)

    .. note::

        There are some cases where an affine transformation can't be
        inverted. This is why *gdal.InvGeoTransform()* returns a
        *success* flag as well as the inverted transformation. With this
        particular set of DEM data, however, the affine transformation
        should always be invertible.

    Now that we have the inverse affine transformation, it is possible to convert from a latitude and longitude into an x and y coordinate, like this:

    .. code-block:: python

       x,y = gdal.ApplyGeoTransform(tInverse, longitude, latitude)
    
    Using this, we can finally identify the minimum and maximum (x, y) coordinates that cover the area we are interested in:

    .. code-block:: python

       x1,y1 = gdal.ApplyGeoTransform(tInverse, minLong, minLat)
       x2,y2 = gdal.ApplyGeoTransform(tInverse, maxLong, maxLat)

       minX = int(min(x1, x2))
       maxX = int(max(x1, x2))
       minY = int(min(y1, y2))
       maxY = int(max(y1, y2))
    
    Now that we know the x and y coordinates for the portion of the DEM that we're
    interested in, we can use GDAL to read in the individual height values. We start
    by obtaining the raster band that contains the DEM data:

    .. code-block:: python

       band = dataset.GetRasterBand(1)

    .. note::

        GDAL band numbers start at one. There is only one raster band in the DEM data we're using.

    Now that we have the raster band, we can use the *band.ReadRaster()* method to read the raw DEM data. This is what the *ReadRaster()* method looks like:

    .. code-block:: python

       band.ReadRaster(x, y, width, height, dWidth, dHeight, pixelType)

    This method takes the following parameters:

    - *x* is the number of pixels from the left-hand side of the raster band to the left-hand side of the portion of the band to read from
    - *y* is the number of pixels from the top of the raster band to the top of the portion of the band to read from
    - *width* is the number of pixels across to read
    - *height* is the number of pixels down to read
    - *dWidth* is the width of the resulting data
    - *dHeight* is the height of the resulting data
    - *pixelType* is a constant defining how many bytes of data there are for each pixel value, and how that data is to be interpreted

    .. note::

        Normally, you would set dWidth and dHeight to the same value as width and height; if you don't do this, the raster data will be scaled up or down when it is read.

    The ReadRaster() method returns a string containing the raster data as a raw
    sequence of bytes. You can then read the individual integer height values from
    this string using the struct standard library module:

    .. code-block:: python

       values = struct.unpack("<" + ("h" * width), data)

    .. note::

        Notice that we use the h format code to read through the data, treating each pair of bytes as a signed 16-bit integer. The < format code forces the use of little-endian byte order. This matches the format used by the DEM file.

    Putting all this together, we can use GDAL to open the raster data file and read all
    the pixel values within the bounding box surrounding New Zealand:

    .. code-block:: python

       # histogram.py

       import sys, struct
       from osgeo import gdal
       from osgeo import gdalconst

       minLat = -48
       maxLat = -33
       
       minLong = 165
       maxLong = 179

       dataset = gdal.Open("l10g")
       band = dataset.GetRasterBand(1)

       t = dataset.GetGeoTransform()
       success,tInverse = gdal.InvGeoTransform(t)
       if not success:
           print "Failed!"
           sys.exit(1)

       x1,y1 = gdal.ApplyGeoTransform(tInverse, minLong, minLat)
       x2,y2 = gdal.ApplyGeoTransform(tInverse, maxLong, maxLat)

       minX = int(min(x1, x2))
       maxX = int(max(x1, x2))
       minY = int(min(y1, y2))
       maxY = int(max(y1, y2))

       width = (maxX - minX) + 1
       fmt = "<" + ("h" * width)

       for y in range(minY, maxY+1):
           scanline = band.ReadRaster(minX, y,width, 1,
                                      width, 1,
                                      gdalconst.GDT_Int16)
           values = struct.unpack(fmt, scanline)

       for value in values:

    .. note::

        Don't forget to add a directory path to the gdal.Open() statement if you placed the l10g file in a different directory.

    .. code-block:: python

       width = (maxX - minX) + 1
       fmt = "<" + ("h" * width)
        
       histogram = {} # Maps height to # pixels with that height.

       for y in range(minY, maxY+1):
           scanline = band.ReadRaster(minX, y,width, 1,
                                      width, 1,
                                      gdalconst.GDT_Int16)
           values = struct.unpack(fmt, scanline)

           for value in values:
               try:
                   histogram[value] += 1
               except KeyError:
                   histogram[value] = 1

       for height in sorted(histogram.keys()):
           print height,histogram[height]
    
    If you run this, you will see a list of heights (in meters) and how many pixels there are at that height:

    .. code-block:: txt

       -500 2607581
       1 6641
       2 909
       3 1628
       ...
       3097 1
       3119 2
       3173 1

    This reveals one final problem: there are a large number of pixels with a value of
    -500. What is going on here? Clearly -500 is not a valid height value. The GLOBE
    documentation explains this as follows:

    .. code-block:: txt

       "Every tile contains values of -500 for oceans, with no values between -500 and the
       minimum value for land noted here."

    So all those points with a value of -500 represents pixels over the ocean. Fortunately,
    it is easy to exclude these; every raster file includes the concept of a no data value,
    which is used for pixels without valid data. GDAL includes the GetNoDataValue()
    method that allows us to exclude these pixels:

    .. code-block:: python

       for value in values:
           if value != band.GetNoDataValue():
               try:
                   histogram[value] += 1
               except KeyError:
                   histogram[value] = 1
    
    This finally gives us a histogram of the heights across New Zealand. You could create a graph using this data if you wished. For example, the following chart shows the total number of pixels at or below a given height:

    .. image:: ./img/143-0.png
       :scale: 50
       :class: with-border
       :align: center

.. tab:: 英文

    A **Digital Elevation Map (DEM)** is a raster geospatial data format where each pixel
    value represents the height of a point on the Earth's surface. We encountered DEM
    files in the previous chapter, where we saw two examples of data sources that supply
    this type of information: the National Elevation Dataset covering the United States,
    and GLOBE which provides DEM files covering the entire Earth.

    Because a DEM file contains height data, it can be interesting to analyze the height
    values for a given area. For example, we could draw a histogram showing how
    much of a country's area is at a certain elevation. Let's take some DEM data from
    the GLOBE dataset, and calculate a height histogram using that data.

    To keep things simple, we will choose a small country surrounded by ocean:
    New Zealand.

    .. note::

        We're using a small country so that we don't have too much data to work with, and we're using a country surrounded by ocean so that we can check all the points within a bounding box, rather than having to use a polygon to exclude points outside of the country's boundaries.

    To download the DEM data, go to the GLOBE website (http://www.ngdc.noaa.
    gov/mgg/topo/globe.html) and click on the **Get Data Online** hyperlink. We're
    going to use the data already calculated for this area of the world, so click on the
    **Any or all 16 "tiles"** hyperlink. New Zealand is in tile L, so click on the hyperlink
    for this tile to download it.

    The file you download will be called *l10g.zip* (or *l10g.gz* if you chose to download
    the tile in GZIP format). If you decompress it, you will end up with a single file called
    *l10g* containing the raw elevation data.

    By itself, this file isn't very useful—it needs to be georeferenced onto the Earth's
    surface so that you can match up each height value with its position on the Earth.
    To do this, you need to download the associated header file. Unfortunately, the
    GLOBE website makes this rather difficult; the header files for the premade tiles
    can be found at:

    http://www.ngdc.noaa.gov/mgg/topo/elev/esri/hdr

    Download the file named l10g.hdr and place it into the same directory as the l10g
    file you downloaded earlier. You can then read the DEM file using GDAL:

    .. code-block:: python

       from osgeo import gdal
       dataset = gdal.Open("l10g")

    As you must have noticed when you downloaded the l10g tile that this tile covers
    much more than just New Zealand—all of Australia is included, as well as Malaysia,
    Papua New Guinea, and several other East-Asian countries. To work with the height
    data for just New Zealand, we have to be able to identify the relevant portion of the
    raster DEM—that is, the range of x and y coordinates which cover New Zealand.
    We start by looking at a map and identifying the minimum and maximum latitude/
    longitude values which enclose all of New Zealand, but no other country:

    .. image:: ./img/138-0.png
       :scale: 50
       :class: with-border
       :align: center

    Rounded to the nearest whole degree, we get a longitude/latitude bounding box of
    (165, -48)…(179, -33). This is the area we want to scan to cover all of New Zealand.

    There is, however, a problem: the raster data consists of pixels or "cells" identified
    by x and y coordinates, not longitude and latitude values. We have to convert from
    longitudes and latitudes into x and y coordinates. To do this, we need to make use
    of the raster DEM's **affine transformation**.

    If you remember, back in *Chapter 3, Python Libraries for Geospatial Development*, we
    discussed that an affine transformation is a complex system for mapping geographic
    coordinates (latitude and longitude values) into raster (x, y) coordinates. Fortunately
    we don't have to deal with these formulas directly, as GDAL will do it for us. We
    start by obtaining our dataset's affine transformation:

    .. code-block:: python

       t = dataset.GetGeoTransform()

    Using this transformation, we can convert an (x, y) coordinate into its associated
    latitude and longitude value. In this case, however, we want to do the opposite—we
    want to take a latitude and longitude, and calculate the associated x and y coordinate.

    To do this, we have to invert the affine transformation. Once again, GDAL will do
    this for us:

    .. code-block:: python

       success,tInverse = gdal.InvGeoTransform(t)
       if not success:
           print "Failed!"
           sys.exit(1)

    .. note::

        There are some cases where an affine transformation can't be
        inverted. This is why *gdal.InvGeoTransform()* returns a
        *success* flag as well as the inverted transformation. With this
        particular set of DEM data, however, the affine transformation
        should always be invertible.

    Now that we have the inverse affine transformation, it is possible to convert from a latitude and longitude into an x and y coordinate, like this:

    .. code-block:: python

       x,y = gdal.ApplyGeoTransform(tInverse, longitude, latitude)
    
    Using this, we can finally identify the minimum and maximum (x, y) coordinates that cover the area we are interested in:

    .. code-block:: python

       x1,y1 = gdal.ApplyGeoTransform(tInverse, minLong, minLat)
       x2,y2 = gdal.ApplyGeoTransform(tInverse, maxLong, maxLat)

       minX = int(min(x1, x2))
       maxX = int(max(x1, x2))
       minY = int(min(y1, y2))
       maxY = int(max(y1, y2))
    
    Now that we know the x and y coordinates for the portion of the DEM that we're
    interested in, we can use GDAL to read in the individual height values. We start
    by obtaining the raster band that contains the DEM data:

    .. code-block:: python

       band = dataset.GetRasterBand(1)

    .. note::

        GDAL band numbers start at one. There is only one raster band in the DEM data we're using.

    Now that we have the raster band, we can use the *band.ReadRaster()* method to read the raw DEM data. This is what the *ReadRaster()* method looks like:

    .. code-block:: python

       band.ReadRaster(x, y, width, height, dWidth, dHeight, pixelType)

    This method takes the following parameters:

    - *x* is the number of pixels from the left-hand side of the raster band to the left-hand side of the portion of the band to read from
    - *y* is the number of pixels from the top of the raster band to the top of the portion of the band to read from
    - *width* is the number of pixels across to read
    - *height* is the number of pixels down to read
    - *dWidth* is the width of the resulting data
    - *dHeight* is the height of the resulting data
    - *pixelType* is a constant defining how many bytes of data there are for each pixel value, and how that data is to be interpreted

    .. note::

        Normally, you would set dWidth and dHeight to the same value as width and height; if you don't do this, the raster data will be scaled up or down when it is read.

    The ReadRaster() method returns a string containing the raster data as a raw
    sequence of bytes. You can then read the individual integer height values from
    this string using the struct standard library module:

    .. code-block:: python

       values = struct.unpack("<" + ("h" * width), data)

    .. note::

        Notice that we use the h format code to read through the data, treating each pair of bytes as a signed 16-bit integer. The < format code forces the use of little-endian byte order. This matches the format used by the DEM file.

    Putting all this together, we can use GDAL to open the raster data file and read all
    the pixel values within the bounding box surrounding New Zealand:

    .. code-block:: python

       # histogram.py

       import sys, struct
       from osgeo import gdal
       from osgeo import gdalconst

       minLat = -48
       maxLat = -33
       
       minLong = 165
       maxLong = 179

       dataset = gdal.Open("l10g")
       band = dataset.GetRasterBand(1)

       t = dataset.GetGeoTransform()
       success,tInverse = gdal.InvGeoTransform(t)
       if not success:
           print "Failed!"
           sys.exit(1)

       x1,y1 = gdal.ApplyGeoTransform(tInverse, minLong, minLat)
       x2,y2 = gdal.ApplyGeoTransform(tInverse, maxLong, maxLat)

       minX = int(min(x1, x2))
       maxX = int(max(x1, x2))
       minY = int(min(y1, y2))
       maxY = int(max(y1, y2))

       width = (maxX - minX) + 1
       fmt = "<" + ("h" * width)

       for y in range(minY, maxY+1):
           scanline = band.ReadRaster(minX, y,width, 1,
                                      width, 1,
                                      gdalconst.GDT_Int16)
           values = struct.unpack(fmt, scanline)

       for value in values:

    .. note::

        Don't forget to add a directory path to the gdal.Open() statement if you placed the l10g file in a different directory.

    .. code-block:: python

       width = (maxX - minX) + 1
       fmt = "<" + ("h" * width)
        
       histogram = {} # Maps height to # pixels with that height.

       for y in range(minY, maxY+1):
           scanline = band.ReadRaster(minX, y,width, 1,
                                      width, 1,
                                      gdalconst.GDT_Int16)
           values = struct.unpack(fmt, scanline)

           for value in values:
               try:
                   histogram[value] += 1
               except KeyError:
                   histogram[value] = 1

       for height in sorted(histogram.keys()):
           print height,histogram[height]
    
    If you run this, you will see a list of heights (in meters) and how many pixels there are at that height:

    .. code-block:: txt

       -500 2607581
       1 6641
       2 909
       3 1628
       ...
       3097 1
       3119 2
       3173 1

    This reveals one final problem: there are a large number of pixels with a value of
    -500. What is going on here? Clearly -500 is not a valid height value. The GLOBE
    documentation explains this as follows:

    .. code-block:: txt

       "Every tile contains values of -500 for oceans, with no values between -500 and the
       minimum value for land noted here."

    So all those points with a value of -500 represents pixels over the ocean. Fortunately,
    it is easy to exclude these; every raster file includes the concept of a no data value,
    which is used for pixels without valid data. GDAL includes the GetNoDataValue()
    method that allows us to exclude these pixels:

    .. code-block:: python

       for value in values:
           if value != band.GetNoDataValue():
               try:
                   histogram[value] += 1
               except KeyError:
                   histogram[value] = 1
    
    This finally gives us a histogram of the heights across New Zealand. You could create a graph using this data if you wished. For example, the following chart shows the total number of pixels at or below a given height:

    .. image:: ./img/143-0.png
       :scale: 50
       :class: with-border
       :align: center


