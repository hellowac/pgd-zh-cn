执行地理空间计算
============================================

Performing geospatial calculations

.. tab:: 中文

    Shapely is a very capable library for performing various calculations on geospatial data. Let's put it through its paces with a complex, real-world problem.

.. tab:: 英文

    Shapely is a very capable library for performing various calculations on geospatial data. Let's put it through its paces with a complex, real-world problem.

任务 – 识别市区内或附近的公园
----------------------------------------------
Task – identify parks in or near urban areas

.. tab:: 中文

    The US Census Bureau make available a shapefile containing something called **Core Based Statistical Areas (CBSAs)**, which are polygons defining urban areas with a population of 10,000 or more. At the same time, the GNIS website provides lists of place names and other details. Using these two data sources, we will identify any parks within or close to an urban area.

    Because of the volume of data we are dealing with, we will limit our search to California. It would take a very long time to check all the CBSA polygon/place name combinations for the entire United States; it's possible to optimize the program to do this quickly, but this would make the example too complex for our current purposes. Let's start by downloading the necessary data. Go to the TIGER website:

    http://census.gov/geo/www/tiger

    Click on the TIGER/Line Shapefiles link, then follow the Download option for the latest version of the TIGER/Link shapefiles (as of this writing, this is the 2012 version). Select the Web Interface option, and choose Core Based Statistical Areas from the pop-up menu. The shapefile you want is called Metropolitan/Micropolitan Statistical Area; click on this button to download the CBSA data for the entire USA.

    The file you download will have a name similar to tl_XXXX_us_cbsa.zip, where XXXX is the year of the data you've downloaded. Once the file has downloaded, decompress it and place the resulting shapefile into a convenient location so that you can work with it.

    You now need to download the GNIS place name data. Go to the GNIS website:

    http://geonames.usgs.gov/domestic

    Click on the Download Domestic Names hyperlink, and then choose the option
    download all national features in one .zip file. The resulting file will be named
    NationalFile_XXX.zip, where XXX is a date stamp. Decompress the ZIP archive,
    and place the resulting .txt file in a convenient place.

    We're now ready to write the code. Let's start by reading through the CBSA
    urban area shapefile and extracting the polygons that define the boundary of
    each urban area:

    .. code-block:: python

        shapefile = ogr.Open("tl_2009_06_cbsa.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            geometry = feature.GetGeometryRef()

    Using what we learned in the previous section, we can convert this geometry into
    a Shapely object so that we can work with it:

    .. code-block:: python

        wkt = geometry.ExportToWkt()
        shape = shapely.wkt.loads(wkt)

    Next, we need to scan through the NationalFile_XXX.txt file to identify the
    features marked as a park. As we mentioned earlier, this file is too large for us
    to process in its entirety; instead we'll just extract the features for California.
    For each of these features, we want to extract the name of the feature and its
    associated latitude and longitude. Here's how we might do this:

    .. code-block:: python

        f = file("NationalFile_XXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
                if chunks[2] == "Park" and chunks[3] == "CA":
                    name = chunks[1]
                    latitude = float(chunks[9])
                    longitude = float(chunks[10])

    Remember that the GNIS place name database is a "pipe-delimited" text file.
    That's why we have to split the line up using line.rstrip().split("|").

    Now comes the fun part: we need to figure out which parks are within or close to
    each urban area. There are two ways we could do this, either of which will work:

    - We could use the shape.distance() method to calculate the distance between the shape and a Point object representing the park's location:

    .. image:: ./img/160-0.png
       :class: with-border
       :scale: 50
       :align: center

    - We could *dilate* the polygon using the *shape.buffer()* method, and then see if the resulting polygon contained the desired point:

    .. image:: ./img/160-1.png
       :class: with-border
       :scale: 50
       :align: center

    The second option is faster when dealing with a large number of points, as we can
    precalculate the dilated polygons and then use them to compare against each point
    in turn. Let's take this option:

    .. code-block:: python

        # findNearbyParks.py

        from osgeo import ogr
        import shapely.geometry
        import shapely.wkt

        MAX_DISTANCE = 0.1 # Angular distance; approx 10 km.

        print "Loading urban areas..."

        urbanAreas = {} # Maps area name to Shapely polygon.

        shapefile = ogr.Open("tl_2012_us_cbsa.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME")
            geometry = feature.GetGeometryRef()
            shape = shapely.wkt.loads(geometry.ExportToWkt())
            dilatedShape = shape.buffer(MAX_DISTANCE)
            urbanAreas[name] = dilatedShape

        print "Checking parks..."

        f = file("NationalFile_XXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
            if chunks[2] == "Park" and chunks[3] == "CA":
                parkName = chunks[1]
                latitude = float(chunks[9])
                longitude = float(chunks[10])

                pt = shapely.geometry.Point(longitude, latitude)

                for urbanName,urbanArea in urbanAreas.items():
                    if urbanArea.contains(pt):
                        print parkName + " is in or near " + urbanName
        f.close()

    .. note::

        Don't forget to change the name of the NationalFile_XXX.txt file to match the actual name of the file you downloaded. You may also add a path of the references to *tl_2012_us_cbsa.shp* and NationalFile_XXX.txt in your program if you placed these in a different directory.

    Note that our program uses **angular distances** to decide if a park is in or near a given urban area. As we mentioned in *Chapter 2, GIS*, an angular distance is the angle between two lines going out from the center of the Earth to the Earth's surface:

    .. image:: ./img/162-0.png
       :class: with-border
       :scale: 50
       :align: center

    Because we are dealing with data for California, where one degree of angular measurement roughly equals 100 kilometers on the Earth's surface, an angular measurement of 0.1 roughly equals a real distance of 10 km.

    Using angular measurements makes the distance calculation easy and quick to calculate, though it doesn't give an exact distance on the Earth's surface. If your application requires exact distances, you could start by using an angular distance to filter out the features obviously too far away, and then obtain an exact result for the remaining features by calculating the point on the polygon's boundary that is closest to the desired point, and then calculating the linear distance between the two points. You would then discard the points that exceed your desired exact linear distance. Implementing this would be an interesting challenge, though not one we will examine in this book.

.. tab:: 英文

    The US Census Bureau make available a shapefile containing something called **Core Based Statistical Areas (CBSAs)**, which are polygons defining urban areas with a population of 10,000 or more. At the same time, the GNIS website provides lists of place names and other details. Using these two data sources, we will identify any parks within or close to an urban area.

    Because of the volume of data we are dealing with, we will limit our search to California. It would take a very long time to check all the CBSA polygon/place name combinations for the entire United States; it's possible to optimize the program to do this quickly, but this would make the example too complex for our current purposes. Let's start by downloading the necessary data. Go to the TIGER website:

    http://census.gov/geo/www/tiger

    Click on the TIGER/Line Shapefiles link, then follow the Download option for the latest version of the TIGER/Link shapefiles (as of this writing, this is the 2012 version). Select the Web Interface option, and choose Core Based Statistical Areas from the pop-up menu. The shapefile you want is called Metropolitan/Micropolitan Statistical Area; click on this button to download the CBSA data for the entire USA.

    The file you download will have a name similar to tl_XXXX_us_cbsa.zip, where XXXX is the year of the data you've downloaded. Once the file has downloaded, decompress it and place the resulting shapefile into a convenient location so that you can work with it.

    You now need to download the GNIS place name data. Go to the GNIS website:

    http://geonames.usgs.gov/domestic

    Click on the Download Domestic Names hyperlink, and then choose the option
    download all national features in one .zip file. The resulting file will be named
    NationalFile_XXX.zip, where XXX is a date stamp. Decompress the ZIP archive,
    and place the resulting .txt file in a convenient place.

    We're now ready to write the code. Let's start by reading through the CBSA
    urban area shapefile and extracting the polygons that define the boundary of
    each urban area:

    .. code-block:: python

        shapefile = ogr.Open("tl_2009_06_cbsa.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            geometry = feature.GetGeometryRef()

    Using what we learned in the previous section, we can convert this geometry into
    a Shapely object so that we can work with it:

    .. code-block:: python

        wkt = geometry.ExportToWkt()
        shape = shapely.wkt.loads(wkt)

    Next, we need to scan through the NationalFile_XXX.txt file to identify the
    features marked as a park. As we mentioned earlier, this file is too large for us
    to process in its entirety; instead we'll just extract the features for California.
    For each of these features, we want to extract the name of the feature and its
    associated latitude and longitude. Here's how we might do this:

    .. code-block:: python

        f = file("NationalFile_XXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
                if chunks[2] == "Park" and chunks[3] == "CA":
                    name = chunks[1]
                    latitude = float(chunks[9])
                    longitude = float(chunks[10])

    Remember that the GNIS place name database is a "pipe-delimited" text file.
    That's why we have to split the line up using line.rstrip().split("|").

    Now comes the fun part: we need to figure out which parks are within or close to
    each urban area. There are two ways we could do this, either of which will work:

    - We could use the shape.distance() method to calculate the distance between the shape and a Point object representing the park's location:

    .. image:: ./img/160-0.png
       :class: with-border
       :scale: 50
       :align: center

    - We could *dilate* the polygon using the *shape.buffer()* method, and then see if the resulting polygon contained the desired point:

    .. image:: ./img/160-1.png
       :class: with-border
       :scale: 50
       :align: center

    The second option is faster when dealing with a large number of points, as we can
    precalculate the dilated polygons and then use them to compare against each point
    in turn. Let's take this option:

    .. code-block:: python

        # findNearbyParks.py

        from osgeo import ogr
        import shapely.geometry
        import shapely.wkt

        MAX_DISTANCE = 0.1 # Angular distance; approx 10 km.

        print "Loading urban areas..."

        urbanAreas = {} # Maps area name to Shapely polygon.

        shapefile = ogr.Open("tl_2012_us_cbsa.shp")
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME")
            geometry = feature.GetGeometryRef()
            shape = shapely.wkt.loads(geometry.ExportToWkt())
            dilatedShape = shape.buffer(MAX_DISTANCE)
            urbanAreas[name] = dilatedShape

        print "Checking parks..."

        f = file("NationalFile_XXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
            if chunks[2] == "Park" and chunks[3] == "CA":
                parkName = chunks[1]
                latitude = float(chunks[9])
                longitude = float(chunks[10])

                pt = shapely.geometry.Point(longitude, latitude)

                for urbanName,urbanArea in urbanAreas.items():
                    if urbanArea.contains(pt):
                        print parkName + " is in or near " + urbanName
        f.close()

    .. note::

        Don't forget to change the name of the NationalFile_XXX.txt file to match the actual name of the file you downloaded. You may also add a path of the references to *tl_2012_us_cbsa.shp* and NationalFile_XXX.txt in your program if you placed these in a different directory.

    Note that our program uses **angular distances** to decide if a park is in or near a given urban area. As we mentioned in *Chapter 2, GIS*, an angular distance is the angle between two lines going out from the center of the Earth to the Earth's surface:

    .. image:: ./img/162-0.png
       :class: with-border
       :scale: 50
       :align: center

    Because we are dealing with data for California, where one degree of angular measurement roughly equals 100 kilometers on the Earth's surface, an angular measurement of 0.1 roughly equals a real distance of 10 km.

    Using angular measurements makes the distance calculation easy and quick to calculate, though it doesn't give an exact distance on the Earth's surface. If your application requires exact distances, you could start by using an angular distance to filter out the features obviously too far away, and then obtain an exact result for the remaining features by calculating the point on the polygon's boundary that is closest to the desired point, and then calculating the linear distance between the two points. You would then discard the points that exceed your desired exact linear distance. Implementing this would be an interesting challenge, though not one we will examine in this book.
