更改基准和投影
============================================

Changing datums and projections

.. tab:: 中文

   If you remember, in *Chapter 2, GIS*, we discussed that a **datum** is a mathematical model of the Earth's shape, while a **projection** is a way of translating points on the Earth's surface into points on a two-dimensional map. There are a large number of available datums and projections—whenever you are working with geospatial data, you must know which datum and which projection (if any) your data uses. If you are combining data from multiple sources, you will often have to change your geospatial data from one datum to another, or from one projection to another.

.. tab:: 英文

   If you remember, in *Chapter 2, GIS*, we discussed that a **datum** is a mathematical model of the Earth's shape, while a **projection** is a way of translating points on the Earth's surface into points on a two-dimensional map. There are a large number of available datums and projections—whenever you are working with geospatial data, you must know which datum and which projection (if any) your data uses. If you are combining data from multiple sources, you will often have to change your geospatial data from one datum to another, or from one projection to another.

任务 – 更改投影以使用地理和 UTM 坐标合并 Shapefile
------------------------------------------------------------------------------------------------------
Task – change projections to combine shapefiles using geographic and UTM coordinates

.. tab:: 中文

   In this recipe, we will work with two shapefiles that have different projections. We haven't yet encountered any geospatial data that uses a projection—all the data we've seen so far uses geographic (unprojected) latitude and longitude values. So let's start by downloading some geospatial data in **Universal Transverse Mercator (UTM)** projection.

   The WebGIS website (http://webgis.com) provides shapefiles describing land-use and land-cover, called LULC datafiles. For this example, we will download a shapefile for southern Florida (Dade County, to be exact), which uses the Universal Transverse Mercator projection.

   You can download this shapefile from the following URL:

   http://webgis.com/MAPS/fl/lulcutm/miami.zip

   The decompressed directory contains the shapefile, called miami.shp, along with a datum_reference.txt file describing the shapefile's coordinate system. This file tells us the following:

   .. code-block:: txt

      The LULC shape file was generated from the original USGS GIRAS LULC
      file by Lakes Environmental Software.
      Datum: NAD83
      Projection: UTM
      Zone: 17
      Data collection date by U.S.G.S.: 1972
      Reference: http://edcwww.cr.usgs.gov/products/landcover/lulc.html

   So this particular shapefile uses UTM Zone 17 projection, and a datum of NAD83.

   Let's take a second shapefile, this time in geographic coordinates. We'll use the GSHHS shoreline database, which uses the WGS84 datum and geographic (latitude/longitude) coordinates.

   .. note::

      You don't need to download the GSHHS database for this example; while we will display a map overlaying the LULC data on top of the GSHHS data, you only need the LULC shapefile to complete this recipe. Drawing maps such as the one shown in this recipe will be covered in *Chapter 8, Using Python and Mapnik to Produce Maps*.

   We can't directly compare the coordinates in these two shapefiles; the LULC shapefile has coordinates measured in UTM (that is, in meters from a given reference line), while the GSHHS shapefile has coordinates in latitude and longitude values (in decimal degrees):

   .. note::

      LULC:  x=485719.47, y=2783420.62
             x=485779.49, y=2783380.63
             x=486129.65, y=2783010.66
             ...
      GSHHS: x=180.0000, y=68.9938
             x=180.0000, y=65.0338
             x=179.9984, y=65.0337

   Before we can combine these two shapefiles, we first have to convert them to use the same projection. We'll do this by converting the LULC shapefile from UTM-17 to geographic (latitude/longitude) coordinates. Doing this requires us to define a **coordinate transformation** and then apply that transformation to each of the features in the shapefile.

   Here is how you can define a coordinate transformation using OGR:

   .. code-block:: python

      from osgeo import osr

      srcProjection = osr.SpatialReference()
      srcProjection.SetUTM(17)
  
      dstProjection = osr.SpatialReference()
      dstProjection.SetWellKnownGeogCS('WGS84') # Lat/long.
      
      transform = osr.CoordinateTransformation(srcProjection,
                                             dstProjection)

   Using this transformation, we can transform each of the features in the shapefile from UTM projection back into geographic coordinates:

   .. code-block:: python

      for i in range(layer.GetFeatureCount()):
          feature = layer.GetFeature(i)
          geometry = feature.GetGeometryRef()
          geometry.Transform(transform)

   Putting all this together with the techniques we explored earlier for copying the features from one shapefile to another, we end up with the following complete program:

   .. code-block:: python

      # changeProjection.py

      import os, os.path, shutil
      from osgeo import ogr
      from osgeo import osr
      from osgeo import gdal

      # Define the source and destination projections, and a
      # transformation object to convert from one to the other.

      srcProjection = osr.SpatialReference()
      srcProjection.SetUTM(17)

      dstProjection = osr.SpatialReference()
      dstProjection.SetWellKnownGeogCS('WGS84') # Lat/long.

      transform = osr.CoordinateTransformation(srcProjection,
                                               dstProjection)

      # Open the source shapefile.

      srcFile = ogr.Open("miami/miami.shp")
      srcLayer = srcFile.GetLayer(0)

      # Create the dest shapefile, and give it the new projection.

      if os.path.exists("miami-reprojected"):
          shutil.rmtree("miami-reprojected")
      os.mkdir("miami-reprojected")

      driver = ogr.GetDriverByName("ESRI Shapefile")
      dstPath = os.path.join("miami-reprojected", "miami.shp")
      dstFile = driver.CreateDataSource(dstPath)
      dstLayer = dstFile.CreateLayer("layer", dstProjection)
      
      # Reproject each feature in turn.
      
      for i in range(srcLayer.GetFeatureCount()):
          feature = srcLayer.GetFeature(i)
          geometry = feature.GetGeometryRef()

          newGeometry = geometry.Clone()
          newGeometry.Transform(transform)

          feature = ogr.Feature(dstLayer.GetLayerDefn())
          feature.SetGeometry(newGeometry)
          dstLayer.CreateFeature(feature)
          feature.Destroy()

      # All done.

      srcFile.Destroy()
      dstFile.Destroy()

   .. note::

      Note that this example doesn't copy field values into the new shapefile; if your shapefile has metadata, you will want to copy the fields across as you create each new feature. Also, the preceding code assumes that the miami.shp shapefile has been placed into a miami sub-directory; you'll need to change the ogr.Open() statement to use the appropriate path name if you've stored this shapefile in a different place.

   After running this program over the miami.shp shapefile, the coordinates for all the features in the shapefile will have been converted from UTM-17 into geographic coordinates:

   .. code-block:: txt

      Before reprojection:  x=485719.47, y=2783420.62
                            x=485779.49, y=2783380.63
                            x=486129.65, y=2783010.66
                            ...

      After reprojection:   x=-81.1417, y=25.1668
                            x=-81.1411, y=25.1664
                            x=-81.1376, y=25.1631

   To see whether this worked, let's draw a map showing the reprojected LULC data overlaid on the GSHHS shoreline data:

   .. image:: ./img/148-0.png
      :scale: 60
      :class: with-border
      :align: center

   The light gray outlines show the various polygons within the LULC shapefile, while the black outline shows the shoreline as defined by the GLOBE shapefile. Both of these shapefiles now use geographic coordinates, and as you can see the coastlines match exactly.

   .. note::

      If you have been watching closely, you may have noticed that the LULC data is using the NAD83 datum, while the GSHHS data and our reprojected version of the LULC data both use the WGS84 datum. We can do this without error because the two datums are identical for points within North America.

.. tab:: 英文

   In this recipe, we will work with two shapefiles that have different projections. We haven't yet encountered any geospatial data that uses a projection—all the data we've seen so far uses geographic (unprojected) latitude and longitude values. So let's start by downloading some geospatial data in **Universal Transverse Mercator (UTM)** projection.

   The WebGIS website (http://webgis.com) provides shapefiles describing land-use and land-cover, called LULC datafiles. For this example, we will download a shapefile for southern Florida (Dade County, to be exact), which uses the Universal Transverse Mercator projection.

   You can download this shapefile from the following URL:

   http://webgis.com/MAPS/fl/lulcutm/miami.zip

   The decompressed directory contains the shapefile, called miami.shp, along with a datum_reference.txt file describing the shapefile's coordinate system. This file tells us the following:

   .. code-block:: txt

      The LULC shape file was generated from the original USGS GIRAS LULC
      file by Lakes Environmental Software.
      Datum: NAD83
      Projection: UTM
      Zone: 17
      Data collection date by U.S.G.S.: 1972
      Reference: http://edcwww.cr.usgs.gov/products/landcover/lulc.html

   So this particular shapefile uses UTM Zone 17 projection, and a datum of NAD83.

   Let's take a second shapefile, this time in geographic coordinates. We'll use the GSHHS shoreline database, which uses the WGS84 datum and geographic (latitude/longitude) coordinates.

   .. note::

      You don't need to download the GSHHS database for this example; while we will display a map overlaying the LULC data on top of the GSHHS data, you only need the LULC shapefile to complete this recipe. Drawing maps such as the one shown in this recipe will be covered in *Chapter 8, Using Python and Mapnik to Produce Maps*.

   We can't directly compare the coordinates in these two shapefiles; the LULC shapefile has coordinates measured in UTM (that is, in meters from a given reference line), while the GSHHS shapefile has coordinates in latitude and longitude values (in decimal degrees):

   .. note::

      LULC:  x=485719.47, y=2783420.62
             x=485779.49, y=2783380.63
             x=486129.65, y=2783010.66
             ...
      GSHHS: x=180.0000, y=68.9938
             x=180.0000, y=65.0338
             x=179.9984, y=65.0337

   Before we can combine these two shapefiles, we first have to convert them to use the same projection. We'll do this by converting the LULC shapefile from UTM-17 to geographic (latitude/longitude) coordinates. Doing this requires us to define a **coordinate transformation** and then apply that transformation to each of the features in the shapefile.

   Here is how you can define a coordinate transformation using OGR:

   .. code-block:: python

      from osgeo import osr

      srcProjection = osr.SpatialReference()
      srcProjection.SetUTM(17)
  
      dstProjection = osr.SpatialReference()
      dstProjection.SetWellKnownGeogCS('WGS84') # Lat/long.
      
      transform = osr.CoordinateTransformation(srcProjection,
                                             dstProjection)

   Using this transformation, we can transform each of the features in the shapefile from UTM projection back into geographic coordinates:

   .. code-block:: python

      for i in range(layer.GetFeatureCount()):
          feature = layer.GetFeature(i)
          geometry = feature.GetGeometryRef()
          geometry.Transform(transform)

   Putting all this together with the techniques we explored earlier for copying the features from one shapefile to another, we end up with the following complete program:

   .. code-block:: python

      # changeProjection.py

      import os, os.path, shutil
      from osgeo import ogr
      from osgeo import osr
      from osgeo import gdal

      # Define the source and destination projections, and a
      # transformation object to convert from one to the other.

      srcProjection = osr.SpatialReference()
      srcProjection.SetUTM(17)

      dstProjection = osr.SpatialReference()
      dstProjection.SetWellKnownGeogCS('WGS84') # Lat/long.

      transform = osr.CoordinateTransformation(srcProjection,
                                               dstProjection)

      # Open the source shapefile.

      srcFile = ogr.Open("miami/miami.shp")
      srcLayer = srcFile.GetLayer(0)

      # Create the dest shapefile, and give it the new projection.

      if os.path.exists("miami-reprojected"):
          shutil.rmtree("miami-reprojected")
      os.mkdir("miami-reprojected")

      driver = ogr.GetDriverByName("ESRI Shapefile")
      dstPath = os.path.join("miami-reprojected", "miami.shp")
      dstFile = driver.CreateDataSource(dstPath)
      dstLayer = dstFile.CreateLayer("layer", dstProjection)
      
      # Reproject each feature in turn.
      
      for i in range(srcLayer.GetFeatureCount()):
          feature = srcLayer.GetFeature(i)
          geometry = feature.GetGeometryRef()

          newGeometry = geometry.Clone()
          newGeometry.Transform(transform)

          feature = ogr.Feature(dstLayer.GetLayerDefn())
          feature.SetGeometry(newGeometry)
          dstLayer.CreateFeature(feature)
          feature.Destroy()

      # All done.

      srcFile.Destroy()
      dstFile.Destroy()

   .. note::

      Note that this example doesn't copy field values into the new shapefile; if your shapefile has metadata, you will want to copy the fields across as you create each new feature. Also, the preceding code assumes that the miami.shp shapefile has been placed into a miami sub-directory; you'll need to change the ogr.Open() statement to use the appropriate path name if you've stored this shapefile in a different place.

   After running this program over the miami.shp shapefile, the coordinates for all the features in the shapefile will have been converted from UTM-17 into geographic coordinates:

   .. code-block:: txt

      Before reprojection:  x=485719.47, y=2783420.62
                            x=485779.49, y=2783380.63
                            x=486129.65, y=2783010.66
                            ...

      After reprojection:   x=-81.1417, y=25.1668
                            x=-81.1411, y=25.1664
                            x=-81.1376, y=25.1631

   To see whether this worked, let's draw a map showing the reprojected LULC data overlaid on the GSHHS shoreline data:

   .. image:: ./img/148-0.png
      :scale: 60
      :class: with-border
      :align: center

   The light gray outlines show the various polygons within the LULC shapefile, while the black outline shows the shoreline as defined by the GLOBE shapefile. Both of these shapefiles now use geographic coordinates, and as you can see the coastlines match exactly.

   .. note::

      If you have been watching closely, you may have noticed that the LULC data is using the NAD83 datum, while the GSHHS data and our reprojected version of the LULC data both use the WGS84 datum. We can do this without error because the two datums are identical for points within North America.


任务 – 更改基准以允许合并较旧和较新的 TIGER 数据
--------------------------------------------------------------------------------
Task – change datums to allow older and newer TIGER data to be combined

.. tab:: 中文

    For this example, we will need to obtain some geospatial data that uses the NAD27 datum. This datum dates back to 1927, and was commonly used for North American geospatial analysis up until the 1980s when it was replaced by NAD83.

    ESRI makes available a set of TIGER/Line files from the 2000 US census, converted into shapefile format. These files can be downloaded from:

    http://esri.com/data/download/census2000-tigerline/index.html

    For the 2000 census data, the TIGER/Line files were all in NAD83, with the exception of Alaska which used the older NAD27 datum. So we can use the preceding site to download a shapefile containing features in NAD27. Go to the site, click on the **Preview and Download** hyperlink, and then choose **Alaska** from the drop-down menu. Select the **Line Features - Roads** layer, then click on the **Submit Selection** button.

    This data is divided up into individual counties. Click on the checkbox beside **Anchorage**, then click on the **Proceed to Download** button to download the shapefile containing road details in Anchorage. The resulting shapefile will be named tgr02020lkA.shp, and will be in a directory called lkA02020.

    As described on the website, this data uses the NAD27 datum. If we were to assume this shapefile used the WSG83 datum, all the features would be in the wrong place:

    .. image:: ./img/149-0.png
       :align: center
       :class: with-border
       :scale: 50

    To make the features appear in the correct place, and to be able to combine these features with other data that uses the WGS84 datum, we need to convert the shapefile to use WGS84. Changing a shapefile from one datum to another requires the same basic process we used earlier to change a shapefile from one projection to another: first you choose the source and destination datums, and define a coordinate transformation to convert from one to the other:

    .. code-block:: python

       srcDatum = osr.SpatialReference()
       srcDatum.SetWellKnownGeogCS('NAD27')

       dstDatum = osr.SpatialReference()
       dstDatum.SetWellKnownGeogCS('WGS84')

       transform = osr.CoordinateTransformation(srcDatum, dstDatum)

    You then process each feature in the shapefile, transforming the feature's geometry using the coordinate transformation:

    .. code-block:: python

        for i in range(srcLayer.GetFeatureCount()):
            feature = srcLayer.GetFeature(i)
            geometry = feature.GetGeometryRef()
            geometry.Transform(transform)

    Here is the complete Python program to convert the lkA02020 shapefile from the NAD27 datum to WGS84:

    .. code-block:: python

        # changeDatum.py

        import os, os.path, shutil
        from osgeo import ogr
        from osgeo import osr
        from osgeo import gdal

        # Define the source and destination datums, and a
        # transformation object to convert from one to the other.

        srcDatum = osr.SpatialReference()
        srcDatum.SetWellKnownGeogCS('NAD27')

        dstDatum = osr.SpatialReference()
        dstDatum.SetWellKnownGeogCS('WGS84')

        transform = osr.CoordinateTransformation(srcDatum, dstDatum)

        # Open the source shapefile.

        srcFile = ogr.Open("lkA02020/tgr02020lkA.shp")
        srcLayer = srcFile.GetLayer(0)

        # Create the dest shapefile, and give it the new projection.

        if os.path.exists("lkA-reprojected"):
            shutil.rmtree("lkA-reprojected")
        os.mkdir("lkA-reprojected")

        driver = ogr.GetDriverByName("ESRI Shapefile")
        dstPath = os.path.join("lkA-reprojected", "lkA02020.shp")
        dstFile = driver.CreateDataSource(dstPath)
        dstLayer = dstFile.CreateLayer("layer", dstDatum)
        
        # Reproject each feature in turn.
        
        for i in range(srcLayer.GetFeatureCount()):
            feature = srcLayer.GetFeature(i)
            geometry = feature.GetGeometryRef()
            newGeometry = geometry.Clone()
            newGeometry.Transform(transform)
            feature = ogr.Feature(dstLayer.GetLayerDefn())
            feature.SetGeometry(newGeometry)
            dstLayer.CreateFeature(feature)
            feature.Destroy()

        # All done.
        
        srcFile.Destroy()
        dstFile.Destroy()

    The preceding code assumes that the *lkA02020* folder is in the same directory as the Python script itself. If you've placed this folder somewhere else, you'll need to change the *ogr.Open()* statement to use the appropriate directory path.

    If we now plot the reprojected features using the WGS84 datum, the features will appear in the correct place:

    .. image:: ./img/152-0.png
       :align: center
       :class: with-border
       :scale: 50

.. tab:: 英文

    For this example, we will need to obtain some geospatial data that uses the NAD27 datum. This datum dates back to 1927, and was commonly used for North American geospatial analysis up until the 1980s when it was replaced by NAD83.

    ESRI makes available a set of TIGER/Line files from the 2000 US census, converted into shapefile format. These files can be downloaded from:

    http://esri.com/data/download/census2000-tigerline/index.html

    For the 2000 census data, the TIGER/Line files were all in NAD83, with the exception of Alaska which used the older NAD27 datum. So we can use the preceding site to download a shapefile containing features in NAD27. Go to the site, click on the **Preview and Download** hyperlink, and then choose **Alaska** from the drop-down menu. Select the **Line Features - Roads** layer, then click on the **Submit Selection** button.

    This data is divided up into individual counties. Click on the checkbox beside **Anchorage**, then click on the **Proceed to Download** button to download the shapefile containing road details in Anchorage. The resulting shapefile will be named tgr02020lkA.shp, and will be in a directory called lkA02020.

    As described on the website, this data uses the NAD27 datum. If we were to assume this shapefile used the WSG83 datum, all the features would be in the wrong place:

    .. image:: ./img/149-0.png
       :align: center
       :class: with-border
       :scale: 50

    To make the features appear in the correct place, and to be able to combine these features with other data that uses the WGS84 datum, we need to convert the shapefile to use WGS84. Changing a shapefile from one datum to another requires the same basic process we used earlier to change a shapefile from one projection to another: first you choose the source and destination datums, and define a coordinate transformation to convert from one to the other:

    .. code-block:: python

       srcDatum = osr.SpatialReference()
       srcDatum.SetWellKnownGeogCS('NAD27')

       dstDatum = osr.SpatialReference()
       dstDatum.SetWellKnownGeogCS('WGS84')

       transform = osr.CoordinateTransformation(srcDatum, dstDatum)

    You then process each feature in the shapefile, transforming the feature's geometry using the coordinate transformation:

    .. code-block:: python

        for i in range(srcLayer.GetFeatureCount()):
            feature = srcLayer.GetFeature(i)
            geometry = feature.GetGeometryRef()
            geometry.Transform(transform)

    Here is the complete Python program to convert the lkA02020 shapefile from the NAD27 datum to WGS84:

    .. code-block:: python

        # changeDatum.py

        import os, os.path, shutil
        from osgeo import ogr
        from osgeo import osr
        from osgeo import gdal

        # Define the source and destination datums, and a
        # transformation object to convert from one to the other.

        srcDatum = osr.SpatialReference()
        srcDatum.SetWellKnownGeogCS('NAD27')

        dstDatum = osr.SpatialReference()
        dstDatum.SetWellKnownGeogCS('WGS84')

        transform = osr.CoordinateTransformation(srcDatum, dstDatum)

        # Open the source shapefile.

        srcFile = ogr.Open("lkA02020/tgr02020lkA.shp")
        srcLayer = srcFile.GetLayer(0)

        # Create the dest shapefile, and give it the new projection.

        if os.path.exists("lkA-reprojected"):
            shutil.rmtree("lkA-reprojected")
        os.mkdir("lkA-reprojected")

        driver = ogr.GetDriverByName("ESRI Shapefile")
        dstPath = os.path.join("lkA-reprojected", "lkA02020.shp")
        dstFile = driver.CreateDataSource(dstPath)
        dstLayer = dstFile.CreateLayer("layer", dstDatum)
        
        # Reproject each feature in turn.
        
        for i in range(srcLayer.GetFeatureCount()):
            feature = srcLayer.GetFeature(i)
            geometry = feature.GetGeometryRef()
            newGeometry = geometry.Clone()
            newGeometry.Transform(transform)
            feature = ogr.Feature(dstLayer.GetLayerDefn())
            feature.SetGeometry(newGeometry)
            dstLayer.CreateFeature(feature)
            feature.Destroy()

        # All done.
        
        srcFile.Destroy()
        dstFile.Destroy()

    The preceding code assumes that the *lkA02020* folder is in the same directory as the Python script itself. If you've placed this folder somewhere else, you'll need to change the *ogr.Open()* statement to use the appropriate directory path.

    If we now plot the reprojected features using the WGS84 datum, the features will appear in the correct place:

    .. image:: ./img/152-0.png
       :align: center
       :class: with-border
       :scale: 50
