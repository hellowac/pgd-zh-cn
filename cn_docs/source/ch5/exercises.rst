练习
============================================

Exercises

.. tab:: 中文

    如果您有兴趣进一步探索本章使用的技术，您可以挑战自己完成以下任务：

    - 修改“计算边界框”计算，以排除偏远岛屿。

    .. hint::

        您可以将每个国家的 MultiPolygon 拆分为单独的 Polygon 对象，然后检查每个多边形的面积，排除那些面积小于给定值的多边形。

    - 使用世界边界数据集创建一个新的 shapefile，其中每个国家由一个包含该国家地理中心的单一“点”几何表示。

    .. hint::

        您可以从我们之前计算的国家边界框开始，然后使用以下公式计算中点：

        .. code-block:: python

            midLat = (minLat + maxLat) / 2
            midLong = (minLong + maxLong) / 2

        对于额外的挑战，您可以使用 Shapely 的 `centroid()` 方法来计算每个国家中心的更精确表示。为此，您需要将国家的轮廓转换为 Shapely 几何体，计算质心，然后将质心转换回 OGR 几何体并保存到输出 shapefile 中。

    - 扩展之前的直方图示例，仅包括位于所选国家轮廓内的高度值。

    .. hint::

        以高效的方式实现这一点可能很困难。一个好的方法是识别构成国家轮廓的每个多边形的边界框，然后遍历该边界框内的 DEM 坐标。您可以检查某个坐标是否真的位于国家的轮廓内，使用 `polygon.contains(point)` 方法，并且只有当点确实在国家的轮廓内时，才将高度添加到直方图中。

    - 优化先前的“识别附近公园”示例，使其能够在处理较大的数据集时快速工作。

    .. hint::

        一种可能性是计算每个城市区域的矩形边界框，然后将该边界框的北、南、东、西边扩展到所需的角度距离。然后，您可以快速排除所有不在该边界框内的点，在进行消耗时间的 `polygon.contains(point)` 调用之前。

    - 计算英国海岸线的总长度。

    .. hint::

        请记住，一个国家的轮廓是一个 MultiPolygon，其中每个 Polygon 在 MultiPolygon 中代表一个单独的岛屿。您需要提取每个岛屿多边形的外环，并计算该外环内线段的总长度。然后，您可以将每个岛屿的长度相加，得到整个国家海岸线的总长度。

    - 设计您自己的可重用地理空间函数库，基于 OGR、GDAL、Shapely 和 pyproj 执行本章讨论的常见操作。

    .. hint::

        编写自己的可重用库模块是一种常见的编程技巧。考虑一下我们在本章中解决的各种任务，以及如何将它们转化为通用的库函数。例如，您可能希望编写一个名为 `calcLineStringLength()` 的函数，该函数接受一个 LineString 并返回该 LineString 所有线段的总长度，在调用 `geod.inv()` 之前，您可以选择将 LineString 的坐标转换为经纬度值。

        然后，您可以编写一个 `calcPolygonOutlineLength()` 函数，利用 `calcLineStringLength()` 来计算一个多边形外环的长度。

.. tab:: 英文

    If you are interested in exploring the techniques used in this chapter further, you might like to challenge yourself with the following tasks:

    - Change the "Calculate Bounding Box" calculation to exclude outlying islands.

    .. hint:: 

        You can split each country's MultiPolygon into individual Polygon objects, and then check the area of each polygon to exclude those which are smaller than a given total value.

    - Use the World Borders Dataset to create a new shapefile, where each country is represented by a single "Point" geometry containing the geographical center of each country.

    .. hint:: 

        You can start with the country bounding boxes we calculated earlier, and then calculate the midpoint using:

        .. code-block:: python

            midLat = (minLat + maxLat) / 2
            midLong = (minLong + maxLong) / 2

        For an extra challenge, you could use Shapely's centroid() method to calculate a more accurate representation of each country's center. To do this, you would have to convert the country's outline into a Shapely geometry, calculate the centroid, and then convert the centroid back into an OGR geometry before saving it into the output shapefile.

    - Extend the preceding histogram example to only include height values that fall inside a selected country's outline.

    .. hint:: 

        Implementing this in an efficient way can be difficult. A good approach would be to identify the bounding box for each of the polygons that make up the country's outline, and then iterate over the DEM coordinates within that bounding box. You could then check to see if a given coordinate is actually inside the country's outline using polygon.contains(point), and only add the height to the histogram if the point is indeed within the country's outline.

    - Optimize the "identify nearby parks" example given earlier so that it can work quickly with larger data sets.

    .. hint:: 

        One possibility might be to calculate the rectangular bounding box around each urban area, and then expand that bounding box north, south, east, and west by the desired angular distance. You could then quickly exclude all the points which aren't in that bounding box before making the time-consuming call to polygon.contains(point).

    - Calculate the total length of the coastline of the United Kingdom.

    .. hint:: 

        Remember that a country outline is a MultiPolygon, where each Polygon in the MultiPolygon represents a single island. You will need to extract the exterior ring from each of these individual island polygons, and calculate the total length of the line segments within that exterior ring. You can then total the length of each individual island to get the length of the entire country's coastline.

    - Design your own reusable library of geospatial functions which build on OGR, GDAL, Shapely, and pyproj to perform common operations such as those discussed in this chapter.

    .. hint:: 

        Writing your own reusable library modules is a common programming tactic. Think about the various tasks we have solved in this chapter, and how they can be turned into generic library functions. For example, you might like to write a function named calcLineStringLength() which takes a LineString and returns the total length of the LineString's segments, optionally transforming the LineString's coordinates into lat/long values before calling geod.inv().

        You could then write a calcPolygonOutlineLength() function which uses calcLineStringLength() to calculate the length of a polygon's outer ring.