8 使用 Python 和 Mapnik 生成地图
============================================

8 Using Python and Mapnik to Generate Maps

.. tab:: 中文

   因为地理空间数据几乎不可能在没有可视化展示之前被理解，所以创建地图以可视化地表示空间数据是一个极其重要的话题。在本章中，我们将探讨Mapnik，这是一个强大的Python库，用于从地理空间数据生成地图。

   本章内容包括：

   - Mapnik 用于生成地图的基本概念
   - 使用 shapefile 内容创建简单地图
   - Mapnik 支持的不同数据源
   - 使用规则、过滤器和样式来控制地图生成过程
   - 使用“符号化器”将线条、多边形、标签、点和栅格图像绘制到地图上
   - 定义地图上使用的颜色
   - 处理地图和图层
   - 设置渲染地图图像的选项
   - 前一章介绍的 mapGenerator.py 模块使用 Mapnik 生成地图
   - 使用地图定义文件来控制和简化地图生成过程

.. tab:: 英文

   Because geospatial data is almost impossible to understand until it is displayed,
   the creation of maps to visually represent spatial data is an extremely important
   topic. In this chapter we will look at Mapnik, a powerful Python library for
   generating maps out of geospatial data.

   This chapter will cover the following:

   - Underlying concepts used by Mapnik to generate maps
   - Creating a simple map using the contents of a shapefile
   - Different data sources that Mapnik supports
   - Using rules, filters, and styles to control the map generation process
   - Using "symbolizers" to draw lines, polygons, labels, points, and raster images onto your map
   - Defining the colors used on a map
   - Working with maps and layers
   - Setting your options for rendering a map image
   - The mapGenerator.py module, introduced in the previous chapter, uses Mapnik to generate maps
   - Using map definition files to control and simplify the map-generation process



.. toctree::
   :maxdepth: 2
   :caption: 内容

   ./introducing.rst
   ./creating.rst
   ./mapnik.rst
   ./mapgenerator.rst
   ./mapdefinition.rst
   ./summary.rst

