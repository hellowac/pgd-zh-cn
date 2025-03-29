小结
============================================

Summary

.. tab:: 中文

    In this chapter, we have implemented, tested, and made improvements to a simple
    web-based application which displays shorelines, towns, and lakes (DISTAL) within
    a given radius of a starting point. This application was the impetus for exploring a
    number of important concepts within geospatial application development, including
    the following:

    - The creation of a simple but complete web-based geospatial application
    - Using databases to store and work with large amounts of geospatial data
    - Using a "black-box" map rendering module to create maps using spatial data selected from a database
    - Examining the issues involved in identifying features based on their true distance rather than using a lat/long approximation
    - Learning how to use spatial joins effectively
    - Exploring usability issues in a prototype implementation
    - Dealing with issues of data quality
    - Learning how to precalculate data to improve performance

    As a result of our development efforts, we have learned the following:

    - Setting up a database and importing large quantities of data from shapefiles and other data sources
    - Designing and structuring a simple web-based application to display maps and respond to user input
    - There are three steps in displaying a map: calculating the lat/long bounding box, calculating the pixel size of the map image, and telling the map renderer which tables to get its data from
    - Given the (x, y) coordinate of a point the user clicked on within a map, how to translate this point into equivalent latitude and longitude value
    - Various ways in which true distance calculations, and selection of features by distance, can be performed
    - Manually calculating distance for every point using the great circle distance formula is accurate but very slow
    - Angular distances (that is, differences in lat/long coordinates) is an easy approximation of distance but doesn't relate in any useful way to true distances across the Earth's surface
    - Using projected coordinates makes true distance calculations easy, but is limited to data covering only part of the Earth's surface
    - Using a hybrid approach to accurately and quickly identify features by distance, by calculating a lat/long bounding box to identify potential features, and then doing a great circle distance calculation on these features to weed out the false positives
    - Setting up a datasource to access and retrieve data from MySQL, PostGIS and SpatiaLite databases
    - Displaying a country's outline and asking the user to click on a desired point works when the country is relatively small and compact, but breaks down for larger countries
    - Learning how issues of data quality can affect the overall usefulness of your geospatial application
    - Learning how you cannot assume that geospatial data comes in the best form for use in your application
    - Very large polygons can degrade performance, and can often be split into smaller subpolygons, resulting in dramatic improvements in performance
    - A divide-and-conquer approach to splitting large polygons is much faster than simply calculating the intersection using the full polygon each time

    In the next chapter, we will explore the details of using the Mapnik library to convert raw geospatial data into map images.

.. tab:: 英文

    In this chapter, we have implemented, tested, and made improvements to a simple
    web-based application which displays shorelines, towns, and lakes (DISTAL) within
    a given radius of a starting point. This application was the impetus for exploring a
    number of important concepts within geospatial application development, including
    the following:

    - The creation of a simple but complete web-based geospatial application
    - Using databases to store and work with large amounts of geospatial data
    - Using a "black-box" map rendering module to create maps using spatial data selected from a database
    - Examining the issues involved in identifying features based on their true distance rather than using a lat/long approximation
    - Learning how to use spatial joins effectively
    - Exploring usability issues in a prototype implementation
    - Dealing with issues of data quality
    - Learning how to precalculate data to improve performance

    As a result of our development efforts, we have learned the following:

    - Setting up a database and importing large quantities of data from shapefiles and other data sources
    - Designing and structuring a simple web-based application to display maps and respond to user input
    - There are three steps in displaying a map: calculating the lat/long bounding box, calculating the pixel size of the map image, and telling the map renderer which tables to get its data from
    - Given the (x, y) coordinate of a point the user clicked on within a map, how to translate this point into equivalent latitude and longitude value
    - Various ways in which true distance calculations, and selection of features by distance, can be performed
    - Manually calculating distance for every point using the great circle distance formula is accurate but very slow
    - Angular distances (that is, differences in lat/long coordinates) is an easy approximation of distance but doesn't relate in any useful way to true distances across the Earth's surface
    - Using projected coordinates makes true distance calculations easy, but is limited to data covering only part of the Earth's surface
    - Using a hybrid approach to accurately and quickly identify features by distance, by calculating a lat/long bounding box to identify potential features, and then doing a great circle distance calculation on these features to weed out the false positives
    - Setting up a datasource to access and retrieve data from MySQL, PostGIS and SpatiaLite databases
    - Displaying a country's outline and asking the user to click on a desired point works when the country is relatively small and compact, but breaks down for larger countries
    - Learning how issues of data quality can affect the overall usefulness of your geospatial application
    - Learning how you cannot assume that geospatial data comes in the best form for use in your application
    - Very large polygons can degrade performance, and can often be split into smaller subpolygons, resulting in dramatic improvements in performance
    - A divide-and-conquer approach to splitting large polygons is much faster than simply calculating the intersection using the full polygon each time

    In the next chapter, we will explore the details of using the Mapnik library to convert raw geospatial data into map images.
