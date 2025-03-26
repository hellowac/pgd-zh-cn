手动处理 GIS 数据
====================================

Working with GIS data manually

.. tab:: 中文

    Let's take a brief look at the process of working with GIS data manually. Before we can begin, there are two things you need to do:

    - Obtain some GIS data
    - Install the GDAL Python library so that you can read the necessary data files

    Let's use the US Census Bureau's website to download a set of vector maps for the
    various US states. The main site for obtaining GIS data from the US Census Bureau
    can be found at:

    http://www.census.gov/geo/www/tiger

    To make things simpler though, let's bypass the website and directly download the
    file we need from the following link:

    http://www2.census.gov/geo/tiger/TIGER2012/STATE/tl_2012_us_state.zip

    The resulting file, tl_2009_us_state.zip, should be a ZIP-format archive. After
    uncompressing the archive, you should have the following files:

    - tl_2012_us_state.dbf
    - tl_2012_us_state.prj
    - tl_2012_us_state.shp
    - tl_2012_us_state.shp.xml
    - tl_2012_us_state.shx

    These files make up a **Shapefile** containing the outlines of all the US states. Place these files together in a convenient directory.

    We next have to download the GDAL Python library. The main website for GDAL can be found at:

    http://gdal.org

    The easiest way to install GDAL onto a Windows or Unix machine is to use the **FWTools installer**, which can be downloaded from the following site:

    http://fwtools.maptools.org

    If you are running Mac OS X, you can find a complete installer for GDAL at:

    http://www.kyngchaos.com/software/frameworks

    After installing GDAL, you can check that it works by typing import osgeo into the Python command prompt; if the Python command prompt reappears with no error message, GDAL was successfully installed and you are all set to go:

    >>> import osgeo
    >>>

    Now that we have some data to work with, let's take a look at it. You can either type the following directly into the command prompt, or else save it as a Python script so that you can run it whenever you wish (let's call this *analyze.py*):

    .. code-block:: python

        import osgeo.ogr

        shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")
        numLayers = shapefile.GetLayerCount()

        print "Shapefile contains %d layers" % numLayers
        print

        for layerNum in range(numLayers):
            layer = shapefile.GetLayer(layerNum)
            spatialRef = layer.GetSpatialRef().ExportToProj4()
            numFeatures = layer.GetFeatureCount()
            print "Layer %d has spatial reference %s" % (layerNum, spatialRef)
            print "Layer %d has %d features:" % (layerNum, numFeatures)
            print

        for featureNum in range(numFeatures):
            feature = layer.GetFeature(featureNum)
            featureName = feature.GetField("NAME")
            
        print "Feature %d has name %s" % (featureNum, featureName)

    .. note::

        The previous example assumes you've placed this script in the same directory as the *tl_2012_us_state.shp* file. If you've put it in a different directory, change the *osgeo.ogr.Open()* command to include the path to your Shapefile. If you are running MS Windows, don't forget to use double backslash characters (\\) as directory separators.

    This gives us a quick summary of how the Shapefile's data is structured:

    .. code-block:: text

        Shapefile contains 1 layers

        Layer 0 has spatial reference +proj=longlat +datum=NAD83 +no_defs
        Layer 0 has 56 features:

        Feature 0 has name Hawaii
        Feature 1 has name Arkansas
        Feature 2 has name New Mexico
        Feature 3 has name Montana
        ...
        Feature 53 has name Arizona
        Feature 54 has name Nevada
        Feature 55 has name California

    This shows us that the data we downloaded consists of one layer, with 56 individual features corresponding to the various states and protectorates in the USA. It also tells us the "spatial reference" for this layer, which tells us that the coordinates are projected as latitude and longitude values using the NAD 83 datum.

    As you can see from the previous example, using GDAL to extract data from Shapefiles is quite straightforward. Let's continue with another example. This time, we'll look at the details for Feature 2, New Mexico:

    .. code-block:: python

        import osgeo.ogr

        shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")

        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(2)

        print "Feature 2 has the following attributes:"
        print

        attributes = feature.items()

        for key,value in attributes.items():
            print " %s = %s" % (key, value)

        geometry = feature.GetGeometryRef()
        geometryName = geometry.GetGeometryName()

        print
        print "Feature's geometry data consists of a %s" % geometryName

    Running this produces the following:

    .. code-block:: text

        Feature 2 has the following attributes:

            DIVISION = 8
            INTPTLAT = +34.4346843
            NAME = New Mexico
            STUSPS = NM
            FUNCSTAT = A
            REGION = 4
            LSAD = 00
            INTPTLON = -106.1316181
            AWATER = 756438507.0
            STATENS = 00897535
            MTFCC = G4000
            STATEFP = 35
            ALAND = 3.14161109357e+11

        Feature's geometry data consists of a POLYGON

    The meaning of the various attributes is described on the US Census Bureau's website, but what interests us right now is the feature's geometry. A geometry object is a complex structure that holds some geospatial data, often using nested geometry objects to reflect the way the geospatial data is organized. So far, we've discovered that New Mexico's geometry consists of a polygon. Let's now take a closer look at this polygon:

    .. code-block:: python

        import osgeo.ogr

        def analyzeGeometry(geometry, indent=0):
            s = []
            s.append(" " * indent)
            s.append(geometry.GetGeometryName())
            if geometry.GetPointCount() > 0:
                s.append(" with %d data points" % geometry.GetPointCount())
            if geometry.GetGeometryCount() > 0:
                s.append(" containing:")
            
            print "".join(s)
            
            for i in range(geometry.GetGeometryCount()):
                analyzeGeometry(geometry.GetGeometryRef(i), indent+1)

        shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")
        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(2)
        geometry = feature.GetGeometryRef()

        analyzeGeometry(geometry)

    The *analyzeGeometry()* function gives a useful idea of how the geometry has been structured:

    .. code-block:: text

        POLYGON containing:
        
            LINEARRING with 7550 data points

    In GDAL (or more specifically the OGR Simple Feature library we are using here), polygons are defined as a single outer "ring" with optional inner rings that define "holes" in the polygon (for example, to show the outline of a lake).

    New Mexico is a relatively simple feature in that it consists of only one polygon. If we ran the same program over California (feature 55 in our Shapefile), the output would be somewhat more complicated:

    .. code-block:: text

        MULTIPOLYGON containing:
            POLYGON containing:
                LINEARRING with 10105 data points
            POLYGON containing:
                LINEARRING with 392 data points
            POLYGON containing:
                LINEARRING with 152 data points
            POLYGON containing:
                LINEARRING with 191 data points
            POLYGON containing:
                LINEARRING with 121 data points
            POLYGON containing:
                LINEARRING with 93 data points
            POLYGON containing:
                LINEARRING with 77 data points

    As you can see, California is made up of seven distinct polygons, each defined by a single linear ring. This is because California is on the coast, and includes six outlying islands as well as the main inland body of the state.

    Let's finish this analysis of the US state Shapefile by answering a simple question: what is the distance from the northernmost point to the southernmost point in California? There are various ways we could answer this question, but for now we'll do it by hand. Let's start by identifying the northernmost and southernmost points in California:

    .. code-block:: python

        import osgeo.ogr

        def findPoints(geometry, results):
            for i in range(geometry.GetPointCount()):
                x,y,z = geometry.GetPoint(i)
                if results['north'] == None or results['north'][1] < y:
                    results['north'] = (x,y)
                if results['south'] == None or results['south'][1] > y:
                    results['south'] = (x,y)
                for i in range(geometry.GetGeometryCount()):
                    findPoints(geometry.GetGeometryRef(i), results)

            shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")
            layer = shapefile.GetLayer(0)
            feature = layer.GetFeature(55)
            geometry = feature.GetGeometryRef()

            results = {'north' : None,
                    'south' : None}

            findPoints(geometry, results)

            print "Northernmost point is (%0.4f, %0.4f)" % results['north']
            print "Southernmost point is (%0.4f, %0.4f)" % results['south']

    The *findPoints()* function recursively scans through a geometry, extracting the individual points and identifying the points with the highest and lowest y (latitude) values, which are then stored in the *results* dictionary so that the main program can use it.

    As you can see, GDAL makes it easy to work with the complex geometry data structure. The code does require recursion, but is still trivial compared with trying to read the data directly. If you run the previous program, the following will be displayed:

    .. code-block:: text

        Northernmost point is (-122.3782, 42.0095)
        Southernmost point is (-117.2049, 32.5288)

    Now that we have these two points, we next want to calculate the distance between them. As described earlier, we have to use a **great circle distance** calculation here to allow for the curvature of the earth's surface. We'll do this manually, using the Haversine formula:

    .. code-block:: python

        import math

        lat1 = 42.0095
        long1 = -122.3782
        
        lat2 = 32.5288
        long2 = -117.2049
        
        rLat1 = math.radians(lat1)
        rLong1 = math.radians(long1)
        rLat2 = math.radians(lat2)
        rLong2 = math.radians(long2)

        dLat = rLat2 - rLat1
        dLong = rLong2 - rLong1
        a = math.sin(dLat/2)**2 + math.cos(rLat1) * math.cos(rLat2) \
                                * math.sin(dLong/2)**2
        c = 2 * math.atan2(math.sqrt(a), math.sqrt(1-a))
        distance = 6371 * c

        print "Great circle distance is %0.0f kilometres" % distance

    Don't worry about the complex maths involved here; basically, we are converting
    the latitude and longitude values to radians, calculating the difference in latitude/
    longitude values between the two points, and then passing the results through some
    trigonometric functions to obtain the great circle distance. The value of 6371 is the
    radius of the earth, in kilometers.

    More details about the Haversine formula and how it is used in the previous example
    can be found at http://mathforum.org/library/drmath/view/51879.html.

    If you run the previous program, your computer will tell you the distance from the
    northernmost point to the southernmost point in California:

    .. code-block:: text

        Great circle distance is 1149 kilometres

    There are, of course, other ways of calculating this. You wouldn't normally type the
    Haversine formula directly into your program, as there are libraries which will do
    this for you. But we deliberately did the calculation this way to show just how it can
    be done.

    If you would like to explore this further, you might like to try writing programs to
    calculate the following:

    - The easternmost and westernmost points in California.
    - The midpoint in California. Hint: you can calculate the midpoint's longitude by taking the average of the easternmost and westernmost longitude.
    - The midpoint in Arizona.
    - The distance between the middle of California and the middle of Arizona.

    As you can see, working with GIS data manually isn't too onerous. While the data structures and maths involved can be rather complex, using tools such as GDAL makes your data accessible and easy to work with.

.. tab:: 英文

    Let's take a brief look at the process of working with GIS data manually. Before we can begin, there are two things you need to do:

    - Obtain some GIS data
    - Install the GDAL Python library so that you can read the necessary data files

    Let's use the US Census Bureau's website to download a set of vector maps for the
    various US states. The main site for obtaining GIS data from the US Census Bureau
    can be found at:

    http://www.census.gov/geo/www/tiger

    To make things simpler though, let's bypass the website and directly download the
    file we need from the following link:

    http://www2.census.gov/geo/tiger/TIGER2012/STATE/tl_2012_us_state.zip

    The resulting file, tl_2009_us_state.zip, should be a ZIP-format archive. After
    uncompressing the archive, you should have the following files:

    - tl_2012_us_state.dbf
    - tl_2012_us_state.prj
    - tl_2012_us_state.shp
    - tl_2012_us_state.shp.xml
    - tl_2012_us_state.shx

    These files make up a **Shapefile** containing the outlines of all the US states. Place these files together in a convenient directory.

    We next have to download the GDAL Python library. The main website for GDAL can be found at:

    http://gdal.org

    The easiest way to install GDAL onto a Windows or Unix machine is to use the **FWTools installer**, which can be downloaded from the following site:

    http://fwtools.maptools.org

    If you are running Mac OS X, you can find a complete installer for GDAL at:

    http://www.kyngchaos.com/software/frameworks

    After installing GDAL, you can check that it works by typing import osgeo into the Python command prompt; if the Python command prompt reappears with no error message, GDAL was successfully installed and you are all set to go:

    >>> import osgeo
    >>>

    Now that we have some data to work with, let's take a look at it. You can either type the following directly into the command prompt, or else save it as a Python script so that you can run it whenever you wish (let's call this *analyze.py*):

    .. code-block:: python

        import osgeo.ogr

        shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")
        numLayers = shapefile.GetLayerCount()

        print "Shapefile contains %d layers" % numLayers
        print

        for layerNum in range(numLayers):
            layer = shapefile.GetLayer(layerNum)
            spatialRef = layer.GetSpatialRef().ExportToProj4()
            numFeatures = layer.GetFeatureCount()
            print "Layer %d has spatial reference %s" % (layerNum, spatialRef)
            print "Layer %d has %d features:" % (layerNum, numFeatures)
            print

        for featureNum in range(numFeatures):
            feature = layer.GetFeature(featureNum)
            featureName = feature.GetField("NAME")
            
        print "Feature %d has name %s" % (featureNum, featureName)

    .. note::

        The previous example assumes you've placed this script in the same directory as the *tl_2012_us_state.shp* file. If you've put it in a different directory, change the *osgeo.ogr.Open()* command to include the path to your Shapefile. If you are running MS Windows, don't forget to use double backslash characters (\\) as directory separators.

    This gives us a quick summary of how the Shapefile's data is structured:

    .. code-block:: text

        Shapefile contains 1 layers

        Layer 0 has spatial reference +proj=longlat +datum=NAD83 +no_defs
        Layer 0 has 56 features:

        Feature 0 has name Hawaii
        Feature 1 has name Arkansas
        Feature 2 has name New Mexico
        Feature 3 has name Montana
        ...
        Feature 53 has name Arizona
        Feature 54 has name Nevada
        Feature 55 has name California

    This shows us that the data we downloaded consists of one layer, with 56 individual features corresponding to the various states and protectorates in the USA. It also tells us the "spatial reference" for this layer, which tells us that the coordinates are projected as latitude and longitude values using the NAD 83 datum.

    As you can see from the previous example, using GDAL to extract data from Shapefiles is quite straightforward. Let's continue with another example. This time, we'll look at the details for Feature 2, New Mexico:

    .. code-block:: python

        import osgeo.ogr

        shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")

        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(2)

        print "Feature 2 has the following attributes:"
        print

        attributes = feature.items()

        for key,value in attributes.items():
            print " %s = %s" % (key, value)

        geometry = feature.GetGeometryRef()
        geometryName = geometry.GetGeometryName()

        print
        print "Feature's geometry data consists of a %s" % geometryName

    Running this produces the following:

    .. code-block:: text

        Feature 2 has the following attributes:

            DIVISION = 8
            INTPTLAT = +34.4346843
            NAME = New Mexico
            STUSPS = NM
            FUNCSTAT = A
            REGION = 4
            LSAD = 00
            INTPTLON = -106.1316181
            AWATER = 756438507.0
            STATENS = 00897535
            MTFCC = G4000
            STATEFP = 35
            ALAND = 3.14161109357e+11

        Feature's geometry data consists of a POLYGON

    The meaning of the various attributes is described on the US Census Bureau's website, but what interests us right now is the feature's geometry. A geometry object is a complex structure that holds some geospatial data, often using nested geometry objects to reflect the way the geospatial data is organized. So far, we've discovered that New Mexico's geometry consists of a polygon. Let's now take a closer look at this polygon:

    .. code-block:: python

        import osgeo.ogr

        def analyzeGeometry(geometry, indent=0):
            s = []
            s.append(" " * indent)
            s.append(geometry.GetGeometryName())
            if geometry.GetPointCount() > 0:
                s.append(" with %d data points" % geometry.GetPointCount())
            if geometry.GetGeometryCount() > 0:
                s.append(" containing:")
            
            print "".join(s)
            
            for i in range(geometry.GetGeometryCount()):
                analyzeGeometry(geometry.GetGeometryRef(i), indent+1)

        shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")
        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(2)
        geometry = feature.GetGeometryRef()

        analyzeGeometry(geometry)

    The *analyzeGeometry()* function gives a useful idea of how the geometry has been structured:

    .. code-block:: text

        POLYGON containing:
        
            LINEARRING with 7550 data points

    In GDAL (or more specifically the OGR Simple Feature library we are using here), polygons are defined as a single outer "ring" with optional inner rings that define "holes" in the polygon (for example, to show the outline of a lake).

    New Mexico is a relatively simple feature in that it consists of only one polygon. If we ran the same program over California (feature 55 in our Shapefile), the output would be somewhat more complicated:

    .. code-block:: text

        MULTIPOLYGON containing:
            POLYGON containing:
                LINEARRING with 10105 data points
            POLYGON containing:
                LINEARRING with 392 data points
            POLYGON containing:
                LINEARRING with 152 data points
            POLYGON containing:
                LINEARRING with 191 data points
            POLYGON containing:
                LINEARRING with 121 data points
            POLYGON containing:
                LINEARRING with 93 data points
            POLYGON containing:
                LINEARRING with 77 data points

    As you can see, California is made up of seven distinct polygons, each defined by a single linear ring. This is because California is on the coast, and includes six outlying islands as well as the main inland body of the state.

    Let's finish this analysis of the US state Shapefile by answering a simple question: what is the distance from the northernmost point to the southernmost point in California? There are various ways we could answer this question, but for now we'll do it by hand. Let's start by identifying the northernmost and southernmost points in California:

    .. code-block:: python

        import osgeo.ogr

        def findPoints(geometry, results):
            for i in range(geometry.GetPointCount()):
                x,y,z = geometry.GetPoint(i)
                if results['north'] == None or results['north'][1] < y:
                    results['north'] = (x,y)
                if results['south'] == None or results['south'][1] > y:
                    results['south'] = (x,y)
                for i in range(geometry.GetGeometryCount()):
                    findPoints(geometry.GetGeometryRef(i), results)

            shapefile = osgeo.ogr.Open("tl_2012_us_state.shp")
            layer = shapefile.GetLayer(0)
            feature = layer.GetFeature(55)
            geometry = feature.GetGeometryRef()

            results = {'north' : None,
                    'south' : None}

            findPoints(geometry, results)

            print "Northernmost point is (%0.4f, %0.4f)" % results['north']
            print "Southernmost point is (%0.4f, %0.4f)" % results['south']

    The *findPoints()* function recursively scans through a geometry, extracting the individual points and identifying the points with the highest and lowest y (latitude) values, which are then stored in the *results* dictionary so that the main program can use it.

    As you can see, GDAL makes it easy to work with the complex geometry data structure. The code does require recursion, but is still trivial compared with trying to read the data directly. If you run the previous program, the following will be displayed:

    .. code-block:: text

        Northernmost point is (-122.3782, 42.0095)
        Southernmost point is (-117.2049, 32.5288)

    Now that we have these two points, we next want to calculate the distance between them. As described earlier, we have to use a **great circle distance** calculation here to allow for the curvature of the earth's surface. We'll do this manually, using the Haversine formula:

    .. code-block:: python

        import math

        lat1 = 42.0095
        long1 = -122.3782
        
        lat2 = 32.5288
        long2 = -117.2049
        
        rLat1 = math.radians(lat1)
        rLong1 = math.radians(long1)
        rLat2 = math.radians(lat2)
        rLong2 = math.radians(long2)

        dLat = rLat2 - rLat1
        dLong = rLong2 - rLong1
        a = math.sin(dLat/2)**2 + math.cos(rLat1) * math.cos(rLat2) \
                                * math.sin(dLong/2)**2
        c = 2 * math.atan2(math.sqrt(a), math.sqrt(1-a))
        distance = 6371 * c

        print "Great circle distance is %0.0f kilometres" % distance

    Don't worry about the complex maths involved here; basically, we are converting
    the latitude and longitude values to radians, calculating the difference in latitude/
    longitude values between the two points, and then passing the results through some
    trigonometric functions to obtain the great circle distance. The value of 6371 is the
    radius of the earth, in kilometers.

    More details about the Haversine formula and how it is used in the previous example
    can be found at http://mathforum.org/library/drmath/view/51879.html.

    If you run the previous program, your computer will tell you the distance from the
    northernmost point to the southernmost point in California:

    .. code-block:: text

        Great circle distance is 1149 kilometres

    There are, of course, other ways of calculating this. You wouldn't normally type the
    Haversine formula directly into your program, as there are libraries which will do
    this for you. But we deliberately did the calculation this way to show just how it can
    be done.

    If you would like to explore this further, you might like to try writing programs to
    calculate the following:

    - The easternmost and westernmost points in California.
    - The midpoint in California. Hint: you can calculate the midpoint's longitude by taking the average of the easternmost and westernmost longitude.
    - The midpoint in Arizona.
    - The distance between the middle of California and the middle of Arizona.

    As you can see, working with GIS data manually isn't too onerous. While the data structures and maths involved can be rather complex, using tools such as GDAL makes your data accessible and easy to work with.