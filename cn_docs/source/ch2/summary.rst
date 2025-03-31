小结
============================================

Summary

.. tab:: 中文

    在本章中，我们讨论了许多构成 GIS 开发的核心概念，简要回顾了 GIS 的历史，研究了常见的 GIS 数据格式，并通过探索从美国人口普查局网站下载的美国州地图，动手操作了相关内容。我们学到了以下内容：

    - 位置通常，但并非总是，通过坐标表示
    - 计算两点之间的距离时，需要考虑地球表面的曲率
    - 必须注意地理空间数据中使用的单位
    - 地图投影将地球表面的三维形状表示为二维地图
    - 地图投影有三种主要类别：圆柱形投影、圆锥形投影和方位角投影
    - 基准面是地球形状的数学模型
    - 使用最广泛的三个基准面分别是 NAD 27、NAD 83 和 WGS 84
    - 坐标系统描述了坐标如何与地球表面上的给定点相关
    - 未投影坐标系统直接表示地球表面上的点
    - 投影坐标系统使用地图投影将地球表示为二维笛卡尔平面，然后将坐标放置到平面上
    - 地理空间数据可以表示点、线段和多边形等形状
    - 你可能会遇到多种标准 GIS 数据格式。有些数据格式适用于栅格数据，而其他则使用矢量数据
    - 使用 Python 手动对 Shapefile 数据执行各种地理空间计算

    在下一章中，我们将更详细地探讨可以用于处理地理空间数据的各种 Python 库。

.. tab:: 英文

    In this chapter, we discussed many of the core concepts that underlie GIS development, looked briefly at the history of GIS, examined some of the more common GIS data formats, and got our hands dirty exploring US state maps downloaded from the US Census Bureau website. We have learned the following:

    - Locations are often, but not always, represented using coordinates
    - Calculating the distance between two points requires you to take into account the curvature of the earth's surface
    - You must be aware of the units used in geospatial data
    - Map projections represent the three-dimensional shape of the earth's surface as a two-dimensional map
    - There are three main classes of map projections: cylindrical, conic and azimuthal
    - Datums are mathematical models of the earth's shape
    - The three most common datums in use are called NAD 27, NAD 83, and WGS 84
    - Coordinate systems describe how coordinates relate to a given point on the earth's surface
    - Unprojected coordinate systems directly represent points on the earth's surface
    - Projected coordinate systems use a map projection to represent the earth as a two-dimensional Cartesian plane, onto which coordinates are then placed
    - Geospatial data can represent shapes in the form of points, linestrings, and polygons
    - There are a number of standard GIS data formats you might encounter. Some data formats work with raster data, while others use vector data
    - Using Python to manually perform various geospatial calculations on Shapefile data

    In the next chapter, we will look in more detail at the various Python libraries which can be used for working with geospatial data.