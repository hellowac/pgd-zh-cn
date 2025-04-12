小结
============================================

Summary

.. tab:: 中文

    在本章中，我们探讨了在 Python 程序中使用 OGR、GDAL、Shapely 和 pyproj 来解决实际问题的各种技术。我们学到了以下内容：

    - 读取和写入 shapefile 中的矢量格式地理空间数据
    - 读取和分析栅格格式地理空间数据
    - 更改 shapefile 使用的基准和投影
    - 使用 Well-Known Text（WKT）格式以纯文本表示地理空间特征和空间参考
    - 使用 WKT 将地理空间数据从一个 Python 库传输到另一个库
    - 使用 WKT 将地理空间数据存储为纯文本格式
    - 使用 Shapely 库对几何体进行各种地理空间计算，包括距离计算、膨胀和交集
    - 使用 pyproj.Proj 类将坐标从一种投影和基准转换为另一种
    - 使用 pyproj.Geod 类将几何坐标转换为距离，反之亦然

    到目前为止，我们编写的程序直接与 shapefile 和其他数据源交互，加载并处理地理空间数据。在下一章中，我们将探讨如何使用数据库存储和处理地理空间数据。这比每次使用时都必须导入的文件存储方式更快且更具可扩展性。

.. tab:: 英文

    In this chapter we have looked at various techniques for using OGR, GDAL, Shapely, and pyproj within Python programs to solve real-world problems. We have learned the following:

    - Reading and writing to vector-format geospatial data in shapefiles
    - Reading and analyzing raster-format geospatial data
    - Changing the datum and projection used by a shapefile
    - Using the Well-Known Text (WKT) format to represent geospatial features and spatial references in plain text
    - Using WKT to transfer geospatial data from one Python library to another
    - Using WKT to store geospatial data in plain text format
    - Using the Shapely library to perform various geospatial calculations on geometries, including distance calculations, dilation, and intersections.
    - Using the pyproj.Proj class to convert coordinates from one projection and datum to another
    - Using the pyproj.Geod class to convert from geometry coordinates to distances, and vice versa

    Up to now, we have written programs that work directly with shapefiles and other data sources to load and then process geospatial data. In the next chapter, we will look at ways in which databases can be used to store and work with geospatial data. This is much faster and more scalable than storing geospatial data in files which have to be imported each time they are used.
