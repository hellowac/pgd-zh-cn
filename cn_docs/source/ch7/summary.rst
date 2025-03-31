小结
============================================

Summary

.. tab:: 中文

    在本章中，我们实现、测试并改进了一个简单的基于 web 的应用程序，该应用程序在给定起点半径范围内显示海岸线、城镇和湖泊（DISTAL）。这个应用程序是我们探索地理空间应用开发中一些重要概念的动力，包括以下内容：

    - 创建一个简单但完整的基于 web 的地理空间应用程序
    - 使用数据库存储和处理大量的地理空间数据
    - 使用“黑箱”地图渲染模块，通过从数据库中选择的空间数据创建地图
    - 考察基于真实距离而非使用纬度/经度近似值来识别特征的问题
    - 学习如何有效使用空间连接
    - 探索原型实现中的可用性问题
    - 处理数据质量问题
    - 学习如何预计算数据以提高性能

    通过我们的开发工作，我们学到了以下内容：

    - 设置数据库并从 shapefile 和其他数据源导入大量数据
    - 设计和构建一个简单的 web 应用程序来显示地图并响应用户输入
    - 显示地图的三个步骤：计算纬度/经度的边界框，计算地图图像的像素大小，以及告诉地图渲染器从哪些表中获取数据
    - 给定用户在地图上点击的点的 (x, y) 坐标，如何将其转换为等效的纬度和经度值
    - 可以执行真实距离计算和按距离选择特征的多种方式
    - 使用大圆距离公式手动计算每个点的距离虽然准确，但非常慢
    - 角度距离（即纬度/经度坐标的差异）是一个简单的距离近似值，但与地球表面上的真实距离没有任何有用的关系
    - 使用投影坐标使真实距离计算变得简单，但仅限于覆盖地球部分表面的数据
    - 使用混合方法，通过计算纬度/经度边界框来准确快速地按距离识别特征，然后对这些特征进行大圆距离计算，以剔除误报
    - 设置数据源以访问和检索 MySQL、PostGIS 和 SpatiaLite 数据库中的数据
    - 显示一个国家的轮廓并要求用户点击一个期望的点，在该国相对较小且紧凑时有效，但对于较大的国家则会失败
    - 学习数据质量问题如何影响地理空间应用程序的整体实用性
    - 学习如何不假设地理空间数据以最适合应用程序的形式存在
    - 非常大的多边形可能会降低性能，通常可以将其拆分成较小的子多边形，从而显著提高性能
    - 使用分而治之的方法拆分大多边形比每次都使用完整的多边形计算交集要快得多

    在下一章中，我们将探讨使用 Mapnik 库将原始地理空间数据转换为地图图像的详细内容。

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
