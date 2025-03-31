小结
============================================

Summary

.. tab:: 中文

    在本章中，我们学习了使用 Python 开发地理空间应用程序的多个重要库。我们了解了以下内容：

    - **GDAL** 是一个用于读取（有时也写入）基于栅格的地理空间数据的 C++ 库。
    - **OGR** 是一个用于读取（有时也写入）基于矢量的地理空间数据的 C++ 库。
    - GDAL 和 OGR 包含易于使用的 Python 绑定，并支持大量的数据格式。
    - **PROJ.4** 库及其 Python 包装器 ``pyproj`` 允许您使用任何所需的地图投影和椭球体在地理坐标（地球表面的点）与制图坐标（二维平面上的 x,y 坐标）之间进行转换。
    - ``pyproj`` 中的 Geod 类允许您基于地球表面上的点、给定的距离和角度（方位角）执行各种大地测量计算。
    - 一个名为 Java Topology Suite 的地理空间数据操作库最初是为 Java 开发的，后来被用 C++ 重写为 GEOS，现在有一个名为 **Shapely** 的 Python 接口可供使用。
    - Shapely 使得表示地理空间数据（如点、线串、线环、多点、多线串、多多边形和几何集合）变得更加简单。
    - 除了表示地理空间数据外，这些类还允许执行各种地理空间计算。
    - **Mapnik** 是一个基于地理空间数据制作精美地图的工具。
    - Mapnik 可以使用 XML 样式表来控制地图上显示的元素及其格式。如果需要，您也可以手动创建样式。
    - 每个 Mapnik 样式都有一系列的 **规则**，用于识别要绘制到地图上的特征。
    - 每个 Mapnik 规则都有一系列的 **符号化器**，控制如何绘制选中的特征。

    虽然这些工具非常强大，但在您拥有可以使用的地理空间数据之前，您无法做任何事情。除非幸运地拥有自己的数据源，或者愿意支付大量费用购买商业数据，否则您的唯一选择是利用互联网上免费提供的地理空间数据。下章将讨论这些免费的地理空间数据来源。

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
