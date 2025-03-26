小结
============================================

Summary

.. tab:: 中文

    In this chapter, we looked at a number of important libraries for developing
    geospatial applications using Python. We learned the following:

    - **GDAL** is a C++ library for reading (and sometimes writing) raster-based geospatial data.
    - **OGR** is a C++ library for reading (and sometimes writing) vector-based geospatial data.
    - GDAL and OGR include Python bindings that are easy to use, and support a large number of data formats.
    - The **PROJ.4** library, and its Pythonic ``pyproj`` wrapper, allow you to convert between geographic coordinates (points on the Earth's surface) and cartographic coordinates (x,y coordinates on a two-dimensional plane) using any desired map projection and ellipsoid.
    - The ``pyproj`` Geod class allows you to perform various geodetic calculations based on points on the Earth's surface, a given distance, and a given angle (azimuth).
    - A geospatial data manipulation library called the Java Topology Suite was originally developed for Java. This was then rewritten in C++ under the name GEOS, and there is now a Python interface to GEOS called **Shapely**.
    - Shapely makes it easy to represent geospatial data in the form of Points, LineStrings, LinearRings, Polygons, MultiPoints, MultiLineStrings, MultiPolygons, and GeometryCollections.
    - As well as representing geospatial data, these classes allow you to perform a variety of geospatial calculations.
    - **Mapnik** is a tool for producing good-looking maps based on geospatial data.
    - Mapnik can use an XML stylesheet to control the elements that appear on the map, and how they are formatted. Styles can also be created by hand if you prefer.
    - Each Mapnik style has a list of **Rules** which are used to identify features to draw onto the map.
    - Each Mapnik rule has a list of **Symbolizers** that control how the selected features are drawn.

    While these tools are very powerful, you can't do anything with them until you
    have some geospatial data to work with. Unless you are lucky enough to have
    access to your own source of data, or are willing to pay large sums to purchase
    data commercially, your only choice is to make use of the geospatial data which is
    freely available on the Internet. These freely-available sources of geospatial
    data are the topic of the next chapter.

.. tab:: 英文

    In this chapter, we looked at a number of important libraries for developing
    geospatial applications using Python. We learned the following:

    - **GDAL** is a C++ library for reading (and sometimes writing) raster-based geospatial data.
    - **OGR** is a C++ library for reading (and sometimes writing) vector-based geospatial data.
    - GDAL and OGR include Python bindings that are easy to use, and support a large number of data formats.
    - The **PROJ.4** library, and its Pythonic ``pyproj`` wrapper, allow you to convert between geographic coordinates (points on the Earth's surface) and cartographic coordinates (x,y coordinates on a two-dimensional plane) using any desired map projection and ellipsoid.
    - The ``pyproj`` Geod class allows you to perform various geodetic calculations based on points on the Earth's surface, a given distance, and a given angle (azimuth).
    - A geospatial data manipulation library called the Java Topology Suite was originally developed for Java. This was then rewritten in C++ under the name GEOS, and there is now a Python interface to GEOS called **Shapely**.
    - Shapely makes it easy to represent geospatial data in the form of Points, LineStrings, LinearRings, Polygons, MultiPoints, MultiLineStrings, MultiPolygons, and GeometryCollections.
    - As well as representing geospatial data, these classes allow you to perform a variety of geospatial calculations.
    - **Mapnik** is a tool for producing good-looking maps based on geospatial data.
    - Mapnik can use an XML stylesheet to control the elements that appear on the map, and how they are formatted. Styles can also be created by hand if you prefer.
    - Each Mapnik style has a list of **Rules** which are used to identify features to draw onto the map.
    - Each Mapnik rule has a list of **Symbolizers** that control how the selected features are drawn.

    While these tools are very powerful, you can't do anything with them until you
    have some geospatial data to work with. Unless you are lucky enough to have
    access to your own source of data, or are willing to pay large sums to purchase
    data commercially, your only choice is to make use of the geospatial data which is
    freely available on the Internet. These freely-available sources of geospatial
    data are the topic of the next chapter.
