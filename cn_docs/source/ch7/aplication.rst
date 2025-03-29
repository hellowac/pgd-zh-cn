应用程序审查和改进
============================================

Application review and improvements

.. tab:: 中文

    At this stage, we have a complete implementation of the DISTAL system that works
    as advertised: a user can choose a country, enter a search radius in miles, click on a
    starting point, and see a high-resolution map showing all the place names within the
    desired search radius. We have solved the distance problem, and have all the data
    needed to search for place names anywhere in the world.

    Of course, we aren't finished yet. There are several areas where our DISTAL
    application doesn't work as it should, including the following:

    - Usability
    - Quality
    - Performance

    Let's take a look at each of these issues, and see how we could improve our design
    and implementation of the DISTAL system.

.. tab:: 英文

    At this stage, we have a complete implementation of the DISTAL system that works
    as advertised: a user can choose a country, enter a search radius in miles, click on a
    starting point, and see a high-resolution map showing all the place names within the
    desired search radius. We have solved the distance problem, and have all the data
    needed to search for place names anywhere in the world.

    Of course, we aren't finished yet. There are several areas where our DISTAL
    application doesn't work as it should, including the following:

    - Usability
    - Quality
    - Performance

    Let's take a look at each of these issues, and see how we could improve our design
    and implementation of the DISTAL system.

可用性
---------------------------------------
Usability

.. tab:: 中文

    If you explore the DISTAL application, you will soon discover a major usability problem with some of the countries. For example, if you click on the **United States** in the **Select Country** page, you will be presented with the following map to click on:

    .. image:: ./img/279-0.png
       :scale: 100
       :class: with-border
       :align: center

    Accurately clicking on a desired point using this map would be almost impossible.

    What has gone wrong? The problem here is twofold:

    - The United States outline doesn't just cover the mainland US, but also includes the outlying states of Alaska and Hawaii. This increases the size of the map considerably.
    - Alaska crosses the 180th meridian—the Alaska Peninsula extends beyond 180 degree west, and continues across the Aleutian Islands to finish at Attu Island with a longitude of 172 degree east. Because it crosses the 180th meridian, Alaska appears on both the left and right sides of the world map.

    Because of this, the United States map goes from -180 degree to +180 degree longitude and +18 degree to +72 degree latitude. This map is far too big to be usable.

    Even for countries which aren't split into separate outlying states, and which don't cross the 180th meridian, we can't be assured that the maps will be detailed enough to click on accurately. For example, here is the map for **Canada**:

    .. image:: ./img/280-0.png
       :class: with-border
       :align: center
       :scale: 100

    Because Canada is over 3,000 miles wide, accurately selecting a 10-mile search radius
    by clicking on a point on this map would be an exercise in frustration.

    An obvious solution to these usability issues would be to let the user "zoom in" on a
    desired area of the large-scale map before clicking to select the starting point for the
    search. Thus, for these larger countries, the user would select the country, choose
    which portion of the country to search on, and then click on the desired starting point.

    This doesn't solve the 180th meridian problem, which is somewhat more difficult.
    Ideally, you would identify those countries which cross the 180th meridian and
    reproject them into some other coordinate system that allows their polygons to
    be drawn contiguously.

.. tab:: 英文

    If you explore the DISTAL application, you will soon discover a major usability problem with some of the countries. For example, if you click on the **United States** in the **Select Country** page, you will be presented with the following map to click on:

    .. image:: ./img/279-0.png
       :scale: 100
       :class: with-border
       :align: center

    Accurately clicking on a desired point using this map would be almost impossible.

    What has gone wrong? The problem here is twofold:

    - The United States outline doesn't just cover the mainland US, but also includes the outlying states of Alaska and Hawaii. This increases the size of the map considerably.
    - Alaska crosses the 180th meridian—the Alaska Peninsula extends beyond 180 degree west, and continues across the Aleutian Islands to finish at Attu Island with a longitude of 172 degree east. Because it crosses the 180th meridian, Alaska appears on both the left and right sides of the world map.

    Because of this, the United States map goes from -180 degree to +180 degree longitude and +18 degree to +72 degree latitude. This map is far too big to be usable.

    Even for countries which aren't split into separate outlying states, and which don't cross the 180th meridian, we can't be assured that the maps will be detailed enough to click on accurately. For example, here is the map for **Canada**:

    .. image:: ./img/280-0.png
       :class: with-border
       :align: center
       :scale: 100

    Because Canada is over 3,000 miles wide, accurately selecting a 10-mile search radius
    by clicking on a point on this map would be an exercise in frustration.

    An obvious solution to these usability issues would be to let the user "zoom in" on a
    desired area of the large-scale map before clicking to select the starting point for the
    search. Thus, for these larger countries, the user would select the country, choose
    which portion of the country to search on, and then click on the desired starting point.
    
    This doesn't solve the 180th meridian problem, which is somewhat more difficult.
    Ideally, you would identify those countries which cross the 180th meridian and
    reproject them into some other coordinate system that allows their polygons to
    be drawn contiguously.


质量
---------------------------------------
Quality

.. tab:: 中文

    As you use the DISTAL system, you will quickly notice some quality issues related
    to the underlying data that is being used. We are going to consider two such issues:
    problems with the name data, and problems with the place name lat/long coordinates.

.. tab:: 英文

    As you use the DISTAL system, you will quickly notice some quality issues related
    to the underlying data that is being used. We are going to consider two such issues:
    problems with the name data, and problems with the place name lat/long coordinates.


地名问题
~~~~~~~~~~~~
Place name issues

.. tab:: 中文

    If you look through the list of place names, you'll notice that some of the names have
    double parentheses around them, like this:

    .. code-block:: python
    
        …
        (( Shinavlash ))
        (( Pilur ))
        (( Kaçarat ))
        (( Kaçaj ))
        (( Goricë ))
        (( Lilaj ))
        …
    
    These are names for places which are thought to no longer exist. Also, you will notice
    that some names have the word "historical" in them, surrounded by either square
    brackets or parentheses:

    .. code-block:: python
        …
        Fairbank (historical)
        Kopiljača [historical]
        Hardyville (historical)
        Dorčol (historical)
        Sotos Crossing (historical)
        Dušanovac (historical)
        …

    Obviously, these should also be removed. Filtering out the names, which should
    be excluded from the DISTAL database is relatively straightforward, and could be
    added to our import logic as we read the NationalFile and Geonames files into
    the database.

.. tab:: 英文

    If you look through the list of place names, you'll notice that some of the names have
    double parentheses around them, like this:

    .. code-block:: python
    
        …
        (( Shinavlash ))
        (( Pilur ))
        (( Kaçarat ))
        (( Kaçaj ))
        (( Goricë ))
        (( Lilaj ))
        …
    
    These are names for places which are thought to no longer exist. Also, you will notice
    that some names have the word "historical" in them, surrounded by either square
    brackets or parentheses:

    .. code-block:: python
        …
        Fairbank (historical)
        Kopiljača [historical]
        Hardyville (historical)
        Dorčol (historical)
        Sotos Crossing (historical)
        Dušanovac (historical)
        …
        
    Obviously, these should also be removed. Filtering out the names, which should
    be excluded from the DISTAL database is relatively straightforward, and could be
    added to our import logic as we read the NationalFile and Geonames files into
    the database.


纬度/经度坐标问题
~~~~~~~~~~~~
Lat/Long coordinate problems

.. tab:: 中文

    Consider the following DISTAL map, covering a part of Netherlands:

    .. image:: ./img/282-0.png
       :scale: 40
       :class: with-border
       :align: center

    The placement of the cities look suspiciously regular, as if the cities are neatly stacked into rows and columns. Drawing a grid over this map confirms this suspicion:

    .. image:: ./img/282-1.png
       :class: with-border
       :align: center

    The towns and cities themselves aren't as regularly spaced as this, of course—the
    problem appears to be caused by inaccurately rounded lat/long coordinates within
    the international place name data.

    This doesn't affect the operation of the DISTAL application, but users may be
    suspicious about the quality of the results when the place names are drawn so
    regularly onto the map. The only solution to this problem would be to find a
    source of more accurate coordinate data for international place names.

.. tab:: 英文

    Consider the following DISTAL map, covering a part of Netherlands:

    .. image:: ./img/282-0.png
       :scale: 40
       :class: with-border
       :align: center

    The placement of the cities look suspiciously regular, as if the cities are neatly stacked into rows and columns. Drawing a grid over this map confirms this suspicion:

    .. image:: ./img/282-1.png
       :class: with-border
       :align: center

    The towns and cities themselves aren't as regularly spaced as this, of course—the
    problem appears to be caused by inaccurately rounded lat/long coordinates within
    the international place name data.
    
    This doesn't affect the operation of the DISTAL application, but users may be
    suspicious about the quality of the results when the place names are drawn so
    regularly onto the map. The only solution to this problem would be to find a
    source of more accurate coordinate data for international place names.


性能
---------------------------------------
Performance

.. tab:: 中文

    Our DISTAL application is certainly working, but its performance leaves something
    to be desired. While the selectCountry.py and selectArea.py scripts run quickly,
    it can take up to three seconds for showResults.py to complete. Clearly, this isn't
    good enough: a delay like this is annoying to the user, and would be disastrous for
    the server as soon as it receives more than twenty requests per minute, as it would
    be receiving more requests than it could process.

.. tab:: 英文

    Our DISTAL application is certainly working, but its performance leaves something
    to be desired. While the selectCountry.py and selectArea.py scripts run quickly,
    it can take up to three seconds for showResults.py to complete. Clearly, this isn't
    good enough: a delay like this is annoying to the user, and would be disastrous for
    the server as soon as it receives more than twenty requests per minute, as it would
    be receiving more requests than it could process.


查找问题
~~~~~~~~~~~~
Finding the problem

.. tab:: 中文

    Let's take a look at what is going on here. It's easy to add some basic timing code to showResults.py, like this:

    .. code-block:: python

        import time
        import logging
        logger = logging.getLogger(...)

        start_time = time.time()
        ...
        end_time = time.time()
        logger.debug("Operation took %0.4f seconds" % (end_time – start_time)

    .. note::

        Note that this uses the logging Python standard module to save
        the timing results. Because CGI scripts use stdout for the HTML
        output, we can't use the print statement to print out the results.
        If you want to time your own code, make sure you configure your
        logger (for example, to use a logging.FileHandler) first.

    Running this code reveals where the script is taking most of its time:

    .. code-block:: text
    
        Calculating lat/long coordinate took 0.0110 seconds
        Identifying place names took 0.0088 seconds
        Generating map took 3.0208 seconds
        Building HTML page took 0.0000 seconds

    Clearly the map-generation process is the bottleneck here. Since it only took a
    fraction of a second to generate a map within the selectArea.py script, there's
    nothing inherent in the map-generation process that causes this bottleneck.
    So what has changed?

    It could be that displaying the place names takes a while, but that's unlikely.
    It's far more likely to be caused by the amount of map data that we are displaying:
    the showResults.py script is using high-resolution shoreline outlines taken from
    the GSHHS dataset, rather than the low-resolution country outline taken from the
    World Borders Dataset. To test this theory, we can change the map data being used
    to generate the map, altering showResults.py to use the low-resolution countries
    table instead of the high-resolution shorelines table.

    The result is a dramatic improvement in speed:

    .. code-block:: text

        Generating map took 0.1729 seconds

    So how can we make the map generation in showResults.py faster? The answer lies
    in the nature of the shoreline data and how we are using it. Consider the situation
    where you are identifying points within 10 miles of Le Havre in France:

    .. image:: ./img/284-0.png
       :class: with-border
       :align: center
       :scale: 50

    The high-resolution shoreline image would look like this:

    .. image:: ./img/285-0.png
       :class: with-border
       :align: center
       :scale: 100

    But this section of coastline is actually part of the following GSHHS shoreline feature:

    .. image:: ./img/285-1.png
       :class: with-border
       :align: center
       :scale: 100

    This shoreline polygon is enormous, consisting of over 1.1 million points, and we're
    only displaying a very small part of it.

    Because these shoreline polygons are so big, the map generator needs to read in the
    entire huge polygon and then discard 99 percent of it to get the desired section of
    shoreline. Also, because the polygon bounding boxes are so large, many irrelevant
    polygons are being processed (and then filtered out) when generating the map.
    This is why showResults.py is so slow.

.. tab:: 英文

    Let's take a look at what is going on here. It's easy to add some basic timing code to showResults.py, like this:

    .. code-block:: python

        import time
        import logging
        logger = logging.getLogger(...)

        start_time = time.time()
        ...
        end_time = time.time()
        logger.debug("Operation took %0.4f seconds" % (end_time – start_time)

    .. note::

        Note that this uses the logging Python standard module to save
        the timing results. Because CGI scripts use stdout for the HTML
        output, we can't use the print statement to print out the results.
        If you want to time your own code, make sure you configure your
        logger (for example, to use a logging.FileHandler) first.

    Running this code reveals where the script is taking most of its time:

    .. code-block:: text
    
        Calculating lat/long coordinate took 0.0110 seconds
        Identifying place names took 0.0088 seconds
        Generating map took 3.0208 seconds
        Building HTML page took 0.0000 seconds

    Clearly the map-generation process is the bottleneck here. Since it only took a
    fraction of a second to generate a map within the selectArea.py script, there's
    nothing inherent in the map-generation process that causes this bottleneck.
    So what has changed?

    It could be that displaying the place names takes a while, but that's unlikely.
    It's far more likely to be caused by the amount of map data that we are displaying:
    the showResults.py script is using high-resolution shoreline outlines taken from
    the GSHHS dataset, rather than the low-resolution country outline taken from the
    World Borders Dataset. To test this theory, we can change the map data being used
    to generate the map, altering showResults.py to use the low-resolution countries
    table instead of the high-resolution shorelines table.

    The result is a dramatic improvement in speed:

    .. code-block:: text

        Generating map took 0.1729 seconds

    So how can we make the map generation in showResults.py faster? The answer lies
    in the nature of the shoreline data and how we are using it. Consider the situation
    where you are identifying points within 10 miles of Le Havre in France:

    .. image:: ./img/284-0.png
       :class: with-border
       :align: center
       :scale: 50

    The high-resolution shoreline image would look like this:

    .. image:: ./img/285-0.png
       :class: with-border
       :align: center
       :scale: 100

    But this section of coastline is actually part of the following GSHHS shoreline feature:

    .. image:: ./img/285-1.png
       :class: with-border
       :align: center
       :scale: 100

    This shoreline polygon is enormous, consisting of over 1.1 million points, and we're
    only displaying a very small part of it.

    Because these shoreline polygons are so big, the map generator needs to read in the
    entire huge polygon and then discard 99 percent of it to get the desired section of
    shoreline. Also, because the polygon bounding boxes are so large, many irrelevant
    polygons are being processed (and then filtered out) when generating the map.
    This is why showResults.py is so slow.


提高性能
~~~~~~~~~~~~
Improving performance

.. tab:: 中文

    It is certainly possible to improve the performance of the showResults.py script.
    As we mentioned in the best practices section of the previous chapter, spatial indexes
    work best when working with relatively small geometries—and our shoreline
    polygons are anything but small. However, because the DISTAL application only
    shows points within a certain distance, we can split these enormous polygons into
    "tiles" which are then precalculated and stored in the database.

    Let's say that we're going to impose a limit of 100 miles to the search radius.
    We'll also arbitrarily define the tiles to be one whole degree of latitude high,
    and one whole degree of longitude wide:

    .. image:: ./img/286-0.png
       :class: with-border
       :align: center
       :scale: 50

    .. note::

        Note that we could choose any tile size we like, but have selected
        whole degrees of longitude and latitude to make it easy to
        calculate which tile a given lat/long coordinate is inside. Each tile
        will be given an integer latitude and longitude value, which we'll
        call iLat and iLong. We can then calculate the tile to use for any
        given latitude and longitude like this:

        .. code-block:: python
        
            iLat = int(round(latitude))
            iLong = int(round(longitude))
        
        We can then simply look up the tile with the given iLat and iLong value.

    For each tile, we will use the same technique we used earlier to identify the bounding box of the search radius, to define a rectangle 100 miles north, east, west, and south of the tile:

    .. image:: ./img/287-0.png
       :class: with-border
       :align: center
       :scale: 50

    Using the bounding box, we can calculate the intersection of the shoreline data with this bounding box:

    .. image:: ./img/287-1.png
       :class: with-border
       :align: center
       :scale: 100

    Any search done within the tile's boundary, up to a maximum of 100 miles in any direction, will only display shorelines within this bounding box. We simply store this intersected shoreline into the database, along with the lat/long coordinates for the tile, and tell the map generator to use the appropriate tile's outline to display the desired shoreline.

.. tab:: 英文

    It is certainly possible to improve the performance of the showResults.py script.
    As we mentioned in the best practices section of the previous chapter, spatial indexes
    work best when working with relatively small geometries—and our shoreline
    polygons are anything but small. However, because the DISTAL application only
    shows points within a certain distance, we can split these enormous polygons into
    "tiles" which are then precalculated and stored in the database.

    Let's say that we're going to impose a limit of 100 miles to the search radius.
    We'll also arbitrarily define the tiles to be one whole degree of latitude high,
    and one whole degree of longitude wide:

    .. image:: ./img/286-0.png
       :class: with-border
       :align: center
       :scale: 50

    .. note::

        Note that we could choose any tile size we like, but have selected
        whole degrees of longitude and latitude to make it easy to
        calculate which tile a given lat/long coordinate is inside. Each tile
        will be given an integer latitude and longitude value, which we'll
        call iLat and iLong. We can then calculate the tile to use for any
        given latitude and longitude like this:

        .. code-block:: python
        
            iLat = int(round(latitude))
            iLong = int(round(longitude))
        
        We can then simply look up the tile with the given iLat and iLong value.

    For each tile, we will use the same technique we used earlier to identify the bounding box of the search radius, to define a rectangle 100 miles north, east, west, and south of the tile:

    .. image:: ./img/287-0.png
       :class: with-border
       :align: center
       :scale: 50

    Using the bounding box, we can calculate the intersection of the shoreline data with this bounding box:

    .. image:: ./img/287-1.png
       :class: with-border
       :align: center
       :scale: 100

    Any search done within the tile's boundary, up to a maximum of 100 miles in any direction, will only display shorelines within this bounding box. We simply store this intersected shoreline into the database, along with the lat/long coordinates for the tile, and tell the map generator to use the appropriate tile's outline to display the desired shoreline.


计算平铺海岸线
~~~~~~~~~~~~
Calculating the tiled shorelines

.. tab:: 中文

    Let's write the program that calculates these tiled shorelines. We'll store this program
    as tileShorelines.py. Start by entering the following into this file:

    .. code-block:: python

        import math
        import pyproj
        from shapely.geometry import Polygon
        from shapely.ops import cascaded_union
        import shapely.wkt

        import database
        
        ############################################################
        
        MAX_DISTANCE = 100000 # Maximum search radius, in meters.

    .. note::

        Note that we're importing the database.py module. Because database.py is within the cgi-bin directory, you should place your tileShorelines.py file in this directory.

    We next need a function to calculate the tile bounding boxes. This function,
    *expandRect()*, should take a rectangle defined using lat/long coordinates, and
    expand it in each direction by a given number of meters. Using the techniques we
    have learned, this is straightforward: we can use pyproj to perform an inverse great
    circle calculation to calculate four points the given number of meters north, east,
    south, and west of the starting point. This will give us the desired bounding box.
    Here's what our function will look like:

    .. code-block:: python

        def expandRect(minLat, minLong, maxLat, maxLong, distance):
            geod = pyproj.Geod(ellps="WGS84")
            midLat = (minLat + maxLat) / 2.0
            midLong = (minLong + maxLong) / 2.0

            try:
                availDistance = geod.inv(midLong, maxLat, midLong,
                                         +90)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(midLong, maxLat, 0, distance)
                    maxLat = y
                else:
                    maxLat = +90
            except:
                maxLat = +90 # Can't expand north.

            try:
                availDistance = geod.inv(maxLong, midLat, +180,
                                         midLat)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(maxLong, midLat, 90,
                                         distance)
                    maxLong = x
                else:
                    maxLong = +180
            except:
                maxLong = +180 # Can't expand east.

            try:
                availDistance = geod.inv(midLong, minLat, midLong,
                                         -90)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(midLong, minLat, 180,
                                         distance)
                    minLat = y
                else:
                    minLat = -90
            except:
                minLat = -90 # Can't expand south.

            try:
                availDistance = geod.inv(maxLong, midLat, -180,
                                         midLat)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(minLong, midLat, 270,
                                         distance)
                    minLong = x
                else:
                    minLong = -180
            except:
                minLong = -180 # Can't expand west.
            
            return (minLat, minLong, maxLat, maxLong)

    .. note::

        Note that we've added error-checking here, to allow rectangles close to the north or south pole.

    Using this function, we will be able to calculate the bounding rectangle for a given tile in the following way:

    .. code-block:: python

        minLat,minLong,maxLat,maxLong = expandRect(iLat, iLong,
                                                   iLat+1, iLong+1,
                                                   MAX_DISTANCE)

    Type the expandRect() function into your tileShorelines.py script, placing it
    immediately below the last import statement. With this in place, we're now ready
    to start creating the tiled shorelines.

    As always, we'll be using the database.py module to handle the database-specific
    portions of our program. We'll start with a function to load the shoreline polygons
    into memory. Add the following to the end of your database.py module:

    .. code-block:: python

        def load_shorelines():
            global _cursor

            shorelines = []

            if DB_TYPE == "MySQL":
                _cursor.execute("SELECT AsText(outline) " +
                                "FROM shorelines WHERE level=1")
            elif DB_TYPE == "PostGIS":
                _cursor.execute("SELECT ST_AsText(outline) " +
                                "FROM shorelines WHERE level=1")
            elif DB_TYPE == "SpatiaLite":
                _cursor.execute("SELECT ST_AsText(outline) " +
                                "FROM shorelines WHERE level=1")

            for row in _cursor:
                outline = shapely.wkt.loads(row[0])
                shorelines.append(outline)

            return shorelines

    .. note::

        This implementation of the shoreline tiling algorithm uses a lot of memory. If your computer has less than 2 gigabytes of RAM, you may need to store temporary results in the database. Doing this will of course slow down the tiling process, but it will still work.

    We can now call this function from the tileShorelines.py script to load the
    shoreline polygons into memory. Add the following lines to the end of your program:

    .. code-block:: python
    
        database.open()
        shorelines = database.load_shorelines()
    
    Now that we've loaded the shoreline polygons, we can start calculating the contents
    of each tile. Let's create a list-of-lists which will hold the (possibly clipped) polygons
    that appear within each tile; add the following to the end of your tileShorelines.
    py script:

    .. code-block:: python
        
        tilePolys = []
        for iLat in range(-90, +90):
            tilePolys.append([])
        for iLong in range(-180, +180):
            tilePolys[-1].append([])
        
    For a given iLat/iLong combination, tilePolys[iLat][iLong] will contain a list
    of the shoreline polygons which appear inside that tile.

    We now want to fill the tilePolys array with the portions of the shorelines that
    will appear within each tile. The obvious way to do this is to calculate the polygon
    intersections, like this:

    .. code-block:: python

        shorelineInTile = shoreline.intersection(tileBounds)

    Unfortunately, this approach would take a very long time to calculate—just as the
    map generation takes about 2-3 seconds to calculate the visible portion of a shoreline,
    it takes about 2-3 seconds to perform this intersection on a huge shoreline polygon.
    Because there are 360 x 180 = 64,800 tiles, it would take several days to complete this
    calculation using this naive approach.

    A much faster solution would be to "divide and conquer" the large polygons. We first split the huge shoreline polygon into vertical strips, like this:

    .. image:: ./img/292-0.png
       :class: with-border
       :align: center

    We then split each vertical strip horizontally to give us the individual parts of the polygon, which can be merged into the individual tiles:

    .. image:: ./img/292-1.png
       :class: with-border
       :align: center

    By dividing the huge polygons into strips, and then further dividing each strip,
    the intersection process is much faster. Here is the code which performs this
    intersection; we start by iterating over each shoreline polygon and calculating
    the polygon's bounds:

    For shoreline in shorelines:

    .. code-block:: python

        minLong,minLat,maxLong,maxLat = shoreline.bounds
        minLong = int(math.floor(minLong))
        minLat = int(math.floor(minLat))
        maxLong = int(math.ceil(maxLong))
        maxLat = int(math.ceil(maxLat))

    We then split the polygon into vertical strips:

    .. code-block:: python

        vStrips = []
        for iLong in range(minLong, maxLong+1):

            stripMinLat = minLat
            stripMaxLat = maxLat
            stripMinLong = iLong
            stripMaxLong = iLong + 1

        bMinLat,bMinLong,bMaxLat,bMaxLong = \
            expandRect(stripMinLat, stripMinLong,
                       stripMaxLat, stripMaxLong,
                       MAX_DISTANCE)

        bounds = Polygon([(bMinLong, bMinLat),
                          (bMinLong, bMaxLat),
                          (bMaxLong, bMaxLat),
                          (bMaxLong, bMinLat),
                          (bMinLong, bMinLat)])

        strip = shoreline.intersection(bounds)
        vStrips.append(strip)

    Next, we process each vertical strip, splitting the strip into tile-sized blocks and
    storing it into tilePolys:

    .. code-block:: python
    
        stripNum = 0
        for iLong in range(minLong, maxLong+1):
            vStrip = vStrips[stripNum]
            stripNum = stripNum + 1

            for iLat in range(minLat, maxLat+1):
                bMinLat,bMinLong,bMaxLat,bMaxLong = \
                    expandRect(iLat, iLong, iLat+1, iLong+1,
                               MAX_DISTANCE)
                
                bounds = Polygon([(bMinLong, bMinLat),
                                   (bMinLong, bMaxLat),
                                   (bMaxLong, bMaxLat),
                                   (bMaxLong, bMinLat),
                                   (bMinLong, bMinLat)])

                polygon = vStrip.intersection(bounds)
                if not polygon.is_empty:
                    tilePolys[iLat][iLong].append(polygon)

    We're now ready to save the tiled shorelines back into the database. Before we can do that, we have to create the appropriate database tables. To do this, add the following function to your database.py module:

    .. code-block:: python

        def create_tile_tables():
            global _cursor, _connection

            if DB_TYPE == "MySQL":
                _cursor.execute("""
                    CREATE TABLE IF NOT EXISTS tiled_shorelines (
                        intLat INTEGER,
                        intLong INTEGER,
                        outline GEOMETRY,
                        
                        PRIMARY KEY (intLat, intLong))
                """)
            elif DB_TYPE == "PostGIS":
                _cursor.execute("DROP TABLE IF EXISTS " +
                                "tiled_shorelines")
                _cursor.execute("""
                    CREATE TABLE tiled_shorelines (
                        intLat INTEGER,
                        intLong INTEGER,

                        PRIMARY KEY (intLat, intLong))
                """)
                _cursor.execute("""
                    SELECT AddGeometryColumn('tiled_shorelines',
                                             'outline', 4326,
                                             'GEOMETRY', 2)
                """)
                _cursor.execute("""
                    CREATE INDEX tiledShorelineIndex
                        ON tiled_shorelines
                        USING GIST(outline)
                """)
                elif DB_TYPE == "SpatiaLite":
                    _cursor.execute("DROP TABLE IF EXISTS " +
                                    "tiled_shorelines")
                    _cursor.execute("""
                        CREATE TABLE tiled_shorelines (
                            intLat INTEGER,
                            intLong INTEGER,
                            PRIMARY KEY (intLat, intLong))
                    """)
                    _cursor.execute("""
                        SELECT AddGeometryColumn('tiled_shorelines',
                                                 'outline', 4326,
                                                 'GEOMETRY', 2)
                    """)
                    _cursor.execute("""
                        SELECT CreateSpatialIndex('tiled_shorelines',
                                                  'outline')
                    """)

                _connection.commit()

    We're using the same technique we used earlier to create the countries and
    shorelines tables to create our new tiled_shorelines table. We can now
    call this from our tileShorelines.py program:

    .. code-block:: python

        database.create_tile_tables()

    Because we'll be storing geometries (Polygons or MultiPolygons) into this
    new table, we'll want to define a function to do this for each type of database.
    Add the following to the end of your database.py module:

    .. code-block:: python

        def save_tiled_shoreline(iLat, iLong, outline_wkt):
            global _cursor, _connection

            if DB_TYPE == "MySQL":
                _cursor.execute("INSERT INTO tiled_shorelines " +
                                "(intLat, intLong, outline) " +
                                "VALUES (%s, %s, GeomFromText(%s))",
                                (iLat, iLong, outline_wkt))
            elif DB_TYPE == "PostGIS":
                _cursor.execute("INSERT INTO tiled_shorelines " +
                                "(intLat, intLong, outline) " +
                                "VALUES (%s, %s, " +
                                "ST_GeomFromText(%s, 4326))",
                                (iLat, iLong, outline_wkt))
            elif DB_TYPE == "SpatiaLite":
                _cursor.execute("INSERT INTO tiled_shorelines " +
                                "(intLat, intLong, outline) " +
                                "VALUES (?, ?, " +
                                "ST_GeomFromText(%s, 4326))",
                                (iLat, iLong, outline_wkt))
            _connection.commit()

    Finally, we can combine the list of polygons within each tile into a single Geometry
    object, and save the results into the database. Add the following to the end of
    tileShorelines.py:

    .. code-block:: python

        for iLat in range(-90, +90):
            for iLong in range(-180, +180):
                polygons = tilePolys[iLat][iLong]
                if len(polygons) == 0:
                    outline = Polygon()
                else:
                    outline = shapely.ops.cascaded_union(polygons)
                wkt = shapely.wkt.dumps(outline)

                database.save_tiled_shoreline(iLat, iLong, wkt)

    This completes our program to tile the shorelines. You can run it by typing the
    following command from the command line:

    .. code-block:: shell

        python tileShorelines.py

    Note that it may take an hour or more to complete, because of all the shoreline data
    that needs to be processed.

    The first time you run the program, you might want to replace this line:

    .. code-block:: shell

        for shoreline in shorelines:

    with the following line:

    .. code-block:: shell

        for shoreline in shorelines[1:2]:

    This will let the program finish in only a few minutes so you can make
    sure it's working, before removing the [1:2] and running it over the
    entire shoreline database.

.. tab:: 英文

    Let's write the program that calculates these tiled shorelines. We'll store this program
    as tileShorelines.py. Start by entering the following into this file:

    .. code-block:: python

        import math
        import pyproj
        from shapely.geometry import Polygon
        from shapely.ops import cascaded_union
        import shapely.wkt

        import database
        
        ############################################################
        
        MAX_DISTANCE = 100000 # Maximum search radius, in meters.

    .. note::

        Note that we're importing the database.py module. Because database.py is within the cgi-bin directory, you should place your tileShorelines.py file in this directory.

    We next need a function to calculate the tile bounding boxes. This function,
    *expandRect()*, should take a rectangle defined using lat/long coordinates, and
    expand it in each direction by a given number of meters. Using the techniques we
    have learned, this is straightforward: we can use pyproj to perform an inverse great
    circle calculation to calculate four points the given number of meters north, east,
    south, and west of the starting point. This will give us the desired bounding box.
    Here's what our function will look like:

    .. code-block:: python

        def expandRect(minLat, minLong, maxLat, maxLong, distance):
            geod = pyproj.Geod(ellps="WGS84")
            midLat = (minLat + maxLat) / 2.0
            midLong = (minLong + maxLong) / 2.0

            try:
                availDistance = geod.inv(midLong, maxLat, midLong,
                                         +90)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(midLong, maxLat, 0, distance)
                    maxLat = y
                else:
                    maxLat = +90
            except:
                maxLat = +90 # Can't expand north.

            try:
                availDistance = geod.inv(maxLong, midLat, +180,
                                         midLat)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(maxLong, midLat, 90,
                                         distance)
                    maxLong = x
                else:
                    maxLong = +180
            except:
                maxLong = +180 # Can't expand east.

            try:
                availDistance = geod.inv(midLong, minLat, midLong,
                                         -90)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(midLong, minLat, 180,
                                         distance)
                    minLat = y
                else:
                    minLat = -90
            except:
                minLat = -90 # Can't expand south.

            try:
                availDistance = geod.inv(maxLong, midLat, -180,
                                         midLat)[2]
                if availDistance >= distance:
                    x,y,angle = geod.fwd(minLong, midLat, 270,
                                         distance)
                    minLong = x
                else:
                    minLong = -180
            except:
                minLong = -180 # Can't expand west.
            
            return (minLat, minLong, maxLat, maxLong)

    .. note::

        Note that we've added error-checking here, to allow rectangles close to the north or south pole.

    Using this function, we will be able to calculate the bounding rectangle for a given tile in the following way:

    .. code-block:: python

        minLat,minLong,maxLat,maxLong = expandRect(iLat, iLong,
                                                   iLat+1, iLong+1,
                                                   MAX_DISTANCE)

    Type the expandRect() function into your tileShorelines.py script, placing it
    immediately below the last import statement. With this in place, we're now ready
    to start creating the tiled shorelines.

    As always, we'll be using the database.py module to handle the database-specific
    portions of our program. We'll start with a function to load the shoreline polygons
    into memory. Add the following to the end of your database.py module:

    .. code-block:: python

        def load_shorelines():
            global _cursor

            shorelines = []

            if DB_TYPE == "MySQL":
                _cursor.execute("SELECT AsText(outline) " +
                                "FROM shorelines WHERE level=1")
            elif DB_TYPE == "PostGIS":
                _cursor.execute("SELECT ST_AsText(outline) " +
                                "FROM shorelines WHERE level=1")
            elif DB_TYPE == "SpatiaLite":
                _cursor.execute("SELECT ST_AsText(outline) " +
                                "FROM shorelines WHERE level=1")

            for row in _cursor:
                outline = shapely.wkt.loads(row[0])
                shorelines.append(outline)

            return shorelines

    .. note::

        This implementation of the shoreline tiling algorithm uses a lot of memory. If your computer has less than 2 gigabytes of RAM, you may need to store temporary results in the database. Doing this will of course slow down the tiling process, but it will still work.

    We can now call this function from the tileShorelines.py script to load the
    shoreline polygons into memory. Add the following lines to the end of your program:

    .. code-block:: python
    
        database.open()
        shorelines = database.load_shorelines()
    
    Now that we've loaded the shoreline polygons, we can start calculating the contents
    of each tile. Let's create a list-of-lists which will hold the (possibly clipped) polygons
    that appear within each tile; add the following to the end of your tileShorelines.
    py script:

    .. code-block:: python
        
        tilePolys = []
        for iLat in range(-90, +90):
            tilePolys.append([])
        for iLong in range(-180, +180):
            tilePolys[-1].append([])
        
    For a given iLat/iLong combination, tilePolys[iLat][iLong] will contain a list
    of the shoreline polygons which appear inside that tile.

    We now want to fill the tilePolys array with the portions of the shorelines that
    will appear within each tile. The obvious way to do this is to calculate the polygon
    intersections, like this:

    .. code-block:: python

        shorelineInTile = shoreline.intersection(tileBounds)

    Unfortunately, this approach would take a very long time to calculate—just as the
    map generation takes about 2-3 seconds to calculate the visible portion of a shoreline,
    it takes about 2-3 seconds to perform this intersection on a huge shoreline polygon.
    Because there are 360 x 180 = 64,800 tiles, it would take several days to complete this
    calculation using this naive approach.

    A much faster solution would be to "divide and conquer" the large polygons. We first split the huge shoreline polygon into vertical strips, like this:

    .. image:: ./img/292-0.png
       :class: with-border
       :align: center

    We then split each vertical strip horizontally to give us the individual parts of the polygon, which can be merged into the individual tiles:

    .. image:: ./img/292-1.png
       :class: with-border
       :align: center

    By dividing the huge polygons into strips, and then further dividing each strip,
    the intersection process is much faster. Here is the code which performs this
    intersection; we start by iterating over each shoreline polygon and calculating
    the polygon's bounds:

    For shoreline in shorelines:

    .. code-block:: python

        minLong,minLat,maxLong,maxLat = shoreline.bounds
        minLong = int(math.floor(minLong))
        minLat = int(math.floor(minLat))
        maxLong = int(math.ceil(maxLong))
        maxLat = int(math.ceil(maxLat))

    We then split the polygon into vertical strips:

    .. code-block:: python

        vStrips = []
        for iLong in range(minLong, maxLong+1):

            stripMinLat = minLat
            stripMaxLat = maxLat
            stripMinLong = iLong
            stripMaxLong = iLong + 1

        bMinLat,bMinLong,bMaxLat,bMaxLong = \
            expandRect(stripMinLat, stripMinLong,
                       stripMaxLat, stripMaxLong,
                       MAX_DISTANCE)

        bounds = Polygon([(bMinLong, bMinLat),
                          (bMinLong, bMaxLat),
                          (bMaxLong, bMaxLat),
                          (bMaxLong, bMinLat),
                          (bMinLong, bMinLat)])

        strip = shoreline.intersection(bounds)
        vStrips.append(strip)

    Next, we process each vertical strip, splitting the strip into tile-sized blocks and
    storing it into tilePolys:

    .. code-block:: python
    
        stripNum = 0
        for iLong in range(minLong, maxLong+1):
            vStrip = vStrips[stripNum]
            stripNum = stripNum + 1

            for iLat in range(minLat, maxLat+1):
                bMinLat,bMinLong,bMaxLat,bMaxLong = \
                    expandRect(iLat, iLong, iLat+1, iLong+1,
                               MAX_DISTANCE)
                
                bounds = Polygon([(bMinLong, bMinLat),
                                   (bMinLong, bMaxLat),
                                   (bMaxLong, bMaxLat),
                                   (bMaxLong, bMinLat),
                                   (bMinLong, bMinLat)])

                polygon = vStrip.intersection(bounds)
                if not polygon.is_empty:
                    tilePolys[iLat][iLong].append(polygon)

    We're now ready to save the tiled shorelines back into the database. Before we can do that, we have to create the appropriate database tables. To do this, add the following function to your database.py module:

    .. code-block:: python

        def create_tile_tables():
            global _cursor, _connection

            if DB_TYPE == "MySQL":
                _cursor.execute("""
                    CREATE TABLE IF NOT EXISTS tiled_shorelines (
                        intLat INTEGER,
                        intLong INTEGER,
                        outline GEOMETRY,
                        
                        PRIMARY KEY (intLat, intLong))
                """)
            elif DB_TYPE == "PostGIS":
                _cursor.execute("DROP TABLE IF EXISTS " +
                                "tiled_shorelines")
                _cursor.execute("""
                    CREATE TABLE tiled_shorelines (
                        intLat INTEGER,
                        intLong INTEGER,

                        PRIMARY KEY (intLat, intLong))
                """)
                _cursor.execute("""
                    SELECT AddGeometryColumn('tiled_shorelines',
                                             'outline', 4326,
                                             'GEOMETRY', 2)
                """)
                _cursor.execute("""
                    CREATE INDEX tiledShorelineIndex
                        ON tiled_shorelines
                        USING GIST(outline)
                """)
                elif DB_TYPE == "SpatiaLite":
                    _cursor.execute("DROP TABLE IF EXISTS " +
                                    "tiled_shorelines")
                    _cursor.execute("""
                        CREATE TABLE tiled_shorelines (
                            intLat INTEGER,
                            intLong INTEGER,
                            PRIMARY KEY (intLat, intLong))
                    """)
                    _cursor.execute("""
                        SELECT AddGeometryColumn('tiled_shorelines',
                                                 'outline', 4326,
                                                 'GEOMETRY', 2)
                    """)
                    _cursor.execute("""
                        SELECT CreateSpatialIndex('tiled_shorelines',
                                                  'outline')
                    """)

                _connection.commit()

    We're using the same technique we used earlier to create the countries and
    shorelines tables to create our new tiled_shorelines table. We can now
    call this from our tileShorelines.py program:

    .. code-block:: python

        database.create_tile_tables()

    Because we'll be storing geometries (Polygons or MultiPolygons) into this
    new table, we'll want to define a function to do this for each type of database.
    Add the following to the end of your database.py module:

    .. code-block:: python

        def save_tiled_shoreline(iLat, iLong, outline_wkt):
            global _cursor, _connection

            if DB_TYPE == "MySQL":
                _cursor.execute("INSERT INTO tiled_shorelines " +
                                "(intLat, intLong, outline) " +
                                "VALUES (%s, %s, GeomFromText(%s))",
                                (iLat, iLong, outline_wkt))
            elif DB_TYPE == "PostGIS":
                _cursor.execute("INSERT INTO tiled_shorelines " +
                                "(intLat, intLong, outline) " +
                                "VALUES (%s, %s, " +
                                "ST_GeomFromText(%s, 4326))",
                                (iLat, iLong, outline_wkt))
            elif DB_TYPE == "SpatiaLite":
                _cursor.execute("INSERT INTO tiled_shorelines " +
                                "(intLat, intLong, outline) " +
                                "VALUES (?, ?, " +
                                "ST_GeomFromText(%s, 4326))",
                                (iLat, iLong, outline_wkt))
            _connection.commit()

    Finally, we can combine the list of polygons within each tile into a single Geometry
    object, and save the results into the database. Add the following to the end of
    tileShorelines.py:

    .. code-block:: python

        for iLat in range(-90, +90):
            for iLong in range(-180, +180):
                polygons = tilePolys[iLat][iLong]
                if len(polygons) == 0:
                    outline = Polygon()
                else:
                    outline = shapely.ops.cascaded_union(polygons)
                wkt = shapely.wkt.dumps(outline)

                database.save_tiled_shoreline(iLat, iLong, wkt)

    This completes our program to tile the shorelines. You can run it by typing the
    following command from the command line:

    .. code-block:: shell

        python tileShorelines.py

    Note that it may take an hour or more to complete, because of all the shoreline data
    that needs to be processed.

    The first time you run the program, you might want to replace this line:

    .. code-block:: shell

        for shoreline in shorelines:

    with the following line:

    .. code-block:: shell

        for shoreline in shorelines[1:2]:

    This will let the program finish in only a few minutes so you can make
    sure it's working, before removing the [1:2] and running it over the
    entire shoreline database.


使用平铺海岸线
~~~~~~~~~~~~
Using tiled shorelines

.. tab:: 中文

    All this gives us a new database table, tiled_shorelines, which holds the shoreline
    data split into partly-overlapping tiles:

    .. image:: ./img/297-0.png
       :scale: 50
       :align: center

    Since we can guarantee that all the shoreline data for a given set of search results
    will be within a single tiled_shoreline record, we can modify showResults.py
    (and database.py) to use the tiled shoreline rather than the raw shoreline data.

    To do this, we'll need to modify our datasource dictionary so that Mapnik will know
    which of the shoreline tiles to use. Let's define a new version of the get_shoreline_
    datasource() function which returns a data source which can handle our tiled
    shorelines. Add the following to the end of your database.py module:

    .. code-block:: python

        def get_tiled_shoreline_datasource(iLat, iLong):
            if DB_TYPE == "MySQL":
                vrtFile = os.path.join(os.path.dirname(__file__),
                                       "shorelines.vrt")
                f = file(vrtFile, "w")
                f.write('<OGRVRTDataSource>\n')
                f.write(' <OGRVRTLayer name="shorelines">\n')
                f.write('
                <SrcDataSource>MYSQL:' + MYSQL_DBNAME)
                if MYSQL_USERNAME not in ["", None]:
                    f.write(",user=" + MYSQL_USERNAME)
                if MYSQL_PASSWORD not in ["", None]:
                    f.write(",passwd=" + MYSQL_PASSWORD)
                f.write(',tables=tiled_shorelines</SrcDataSource>\n')
                f.write('   <SrcSQL>\n')
                f.write('   SELECT outline ' +
                            'FROM tiled_shorelines WHERE ' +
                            '(intLat=%d) AND (intlong=%d)' %
                            (iLat, iLong) + '\n')
                f.write('   </SrcSQL>\n')
                f.write(' </OGRVRTLayer>\n')
                f.write('</OGRVRTDataSource>\n')
                f.close()

                return {'type' : "OGR",
                        'file' : vrtFile,
                        'layer' : "shorelines"}
            elif DB_TYPE == "PostGIS":
                sql = "(SELECT outline FROM tiled_shorelines" \
                    + " WHERE (intLat=%d) AND (intLong=%d)) " \
                    % (iLat, iLong) + "AS shorelines"

                return {'type': "PostGIS",
                        'dbname': "distal",
                        'table': sql,
                        'extent_from_subquery' : True,
                        'user': POSTGIS_USERNAME,
                        'password': POSTGIS_PASSWORD}

            elif DB_TYPE == "SpatiaLite":
                sql = "(SELECT outline FROM tiled_shorelines" \
                    + " WHERE (intLat=%d) AND (intLong=%d)) " \
                    % (iLat, iLong) + "AS shorelines"

                return {'type': "SQLite",
                        'file': SPATIALITE_DBNAME,
                        'table': sql,
                        'geometry_field' : "outline",
                        'key_field': "id"}

    We can now use this within our showResults.py script to use the tiled shorelines. To do this, replace the line that says:

    .. code-block:: python

        datasource = database.get_shoreline_datasource()

    with the following code:

    .. code-block:: python

        iLat = int(round(latitude))
        
        iLong = int(round(longitude))
        datasource = database.get_tiled_shoreline_datasource(iLat, iLong)

    With these changes, the showResults.py script will use the tiled shorelines rather than the full shoreline data downloaded from GSHHS. Let's now take a look at how much of a performance improvement these tiled shorelines give us.

.. tab:: 英文

    All this gives us a new database table, tiled_shorelines, which holds the shoreline
    data split into partly-overlapping tiles:

    .. image:: ./img/297-0.png
       :scale: 50
       :align: center

    Since we can guarantee that all the shoreline data for a given set of search results
    will be within a single tiled_shoreline record, we can modify showResults.py
    (and database.py) to use the tiled shoreline rather than the raw shoreline data.

    To do this, we'll need to modify our datasource dictionary so that Mapnik will know
    which of the shoreline tiles to use. Let's define a new version of the get_shoreline_
    datasource() function which returns a data source which can handle our tiled
    shorelines. Add the following to the end of your database.py module:

    .. code-block:: python

        def get_tiled_shoreline_datasource(iLat, iLong):
            if DB_TYPE == "MySQL":
                vrtFile = os.path.join(os.path.dirname(__file__),
                                       "shorelines.vrt")
                f = file(vrtFile, "w")
                f.write('<OGRVRTDataSource>\n')
                f.write(' <OGRVRTLayer name="shorelines">\n')
                f.write('
                <SrcDataSource>MYSQL:' + MYSQL_DBNAME)
                if MYSQL_USERNAME not in ["", None]:
                    f.write(",user=" + MYSQL_USERNAME)
                if MYSQL_PASSWORD not in ["", None]:
                    f.write(",passwd=" + MYSQL_PASSWORD)
                f.write(',tables=tiled_shorelines</SrcDataSource>\n')
                f.write('   <SrcSQL>\n')
                f.write('   SELECT outline ' +
                            'FROM tiled_shorelines WHERE ' +
                            '(intLat=%d) AND (intlong=%d)' %
                            (iLat, iLong) + '\n')
                f.write('   </SrcSQL>\n')
                f.write(' </OGRVRTLayer>\n')
                f.write('</OGRVRTDataSource>\n')
                f.close()

                return {'type' : "OGR",
                        'file' : vrtFile,
                        'layer' : "shorelines"}
            elif DB_TYPE == "PostGIS":
                sql = "(SELECT outline FROM tiled_shorelines" \
                    + " WHERE (intLat=%d) AND (intLong=%d)) " \
                    % (iLat, iLong) + "AS shorelines"

                return {'type': "PostGIS",
                        'dbname': "distal",
                        'table': sql,
                        'extent_from_subquery' : True,
                        'user': POSTGIS_USERNAME,
                        'password': POSTGIS_PASSWORD}

            elif DB_TYPE == "SpatiaLite":
                sql = "(SELECT outline FROM tiled_shorelines" \
                    + " WHERE (intLat=%d) AND (intLong=%d)) " \
                    % (iLat, iLong) + "AS shorelines"

                return {'type': "SQLite",
                        'file': SPATIALITE_DBNAME,
                        'table': sql,
                        'geometry_field' : "outline",
                        'key_field': "id"}

    We can now use this within our showResults.py script to use the tiled shorelines. To do this, replace the line that says:

    .. code-block:: python

        datasource = database.get_shoreline_datasource()

    with the following code:

    .. code-block:: python

        iLat = int(round(latitude))
        
        iLong = int(round(longitude))
        datasource = database.get_tiled_shoreline_datasource(iLat, iLong)

    With these changes, the showResults.py script will use the tiled shorelines rather than the full shoreline data downloaded from GSHHS. Let's now take a look at how much of a performance improvement these tiled shorelines give us.


分析性能改进
~~~~~~~~~~~~
Analyzing performance improvement

.. tab:: 中文

    As soon as you run this new version of the DISTAL application, you'll notice a
    huge improvement in speed: showResults.py now seems to return its results
    almost instantly. Where before the map generator was taking about 2-3 seconds
    to generate the high-resolution maps, it's now only taking a fraction of a second:

    .. code-block:: text

        Generating map took 0.1074 seconds

    That's a dramatic improvement in performance: the map generator is now 15-20
    times faster than it was, and the total time taken by the showResults.py script is
    now less than a quarter of a second. That's not bad for a relatively simple change
    to our underlying map data.

.. tab:: 英文

    As soon as you run this new version of the DISTAL application, you'll notice a
    huge improvement in speed: showResults.py now seems to return its results
    almost instantly. Where before the map generator was taking about 2-3 seconds
    to generate the high-resolution maps, it's now only taking a fraction of a second:

    .. code-block:: text

        Generating map took 0.1074 seconds

    That's a dramatic improvement in performance: the map generator is now 15-20
    times faster than it was, and the total time taken by the showResults.py script is
    now less than a quarter of a second. That's not bad for a relatively simple change
    to our underlying map data.

