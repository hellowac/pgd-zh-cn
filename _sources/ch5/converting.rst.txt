转换和标准化几何和距离单位
==============================================================

Converting and standardizing units of geometry and distance

.. tab:: 中文

    假设你在地球表面上有两个点，并且在它们之间画了一条直线：

    .. image:: ./img/163-0.png
       :class: with-border
       :scale: 50
       :align: center

    每一个点都可以用某种坐标系统来描述（例如，使用纬度和经度值），而直线的长度可以描述为这两个点之间的“距离”。

    .. note::

        当然，由于地球表面不是平的，我们实际上并不处理直线。相反，我们计算的是大地测量或**大圆**距离，即跨越地球表面的距离。

    给定任何两个坐标，实际上是可以计算它们之间的距离的。反过来，你也可以从一个坐标出发，给定一个期望的距离和方向，然后计算另一个点的坐标。

    pyproj Python 库允许你对任何给定的基准进行这些类型的计算。你还可以使用 pyproj 从投影坐标转换回地理坐标，反之亦然，从而使你能够对任何期望的基准、坐标系统和投影进行这些类型的计算。

    最终，一个几何图形，如线或多边形，实际上不过是一组连接点的列表。这意味着，利用前面提到的过程，你可以计算任何多边形中每个点之间的大地测量距离，并将结果相加，从而得到任何几何图形的实际长度。让我们利用这些知识来解决一个现实世界的问题。

.. tab:: 英文

    Imagine that you have two points on the Earth's surface, with a straight line drawn between them:

    .. image:: ./img/163-0.png
       :class: with-border
       :scale: 50
       :align: center

    Each of these points can be described as a coordinate using some arbitrary coordinate system (for example, using latitude and longitude values), while the length of the straight line could be described as the "distance" between the two points.

    .. note::

        Of course, because the Earth's surface is not flat, we aren't really dealing with straight lines at all. Rather, we are calculating geodetic or **Great Circle** distances across the surface of the Earth.

    Given any two coordinates, it is possible to calculate the distance between them. Conversely, you can start with one coordinate, a desired distance and a direction, and then calculate the coordinates for the other point.

    The pyproj Python library allows you to perform these types of calculations for any given datum. You can also use pyproj to convert from projected coordinates back to geographic coordinates, and vice versa, allowing you to perform these sorts of calculations for any desired datum, coordinate system and projection.

    Ultimately, a geometry such as a line or a polygon consists of nothing more than a list of connected points. This means that, using the process mentioned earlier, you can calculate the geodetic distance between each of the points in any polygon and total the results to get the actual length for any geometry. Let's use this knowledge to solve a real-world problem.

任务 - 计算泰缅边界的长度
---------------------------------------------------------
Task – calculate the length of the Thai-Myanmar border

.. tab:: 中文

    为了解决这个问题，我们将使用我们之前创建的 `common-border/border.shp` shapefile。该 shapefile 包含一个单一的特征，这是一个定义两国边界的 `LineString`。让我们首先查看构成该特征几何形状的各个线段：

    .. code-block:: python

        import os.path
        from osgeo import ogr

        def getLineSegmentsFromGeometry(geometry):
            segments = []
            if geometry.GetPointCount() > 0:
                segment = []
                for i in range(geometry.GetPointCount()):
                    segment.append(geometry.GetPoint_2D(i))
                    segments.append(segment)
            for i in range(geometry.GetGeometryCount()):
                subGeometry = geometry.GetGeometryRef(i)
                segments.extend(getLineSegmentsFromGeometry(subGeometry))
            return segments

        filename = os.path.join("common-border", "border.shp")
        shapefile = ogr.Open(filename)
        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(0)
        geometry = feature.GetGeometryRef()

        segments = getLineSegmentsFromGeometry(geometry)

        print segments

    .. note::

        别忘了修改 `os.path.join()` 语句，以匹配你自己的 `border.shp` 文件的位置。

    注意，我们使用了递归函数 *getLineSegmentsFromGeometry()* 来从几何形状中提取每个线段的坐标。因为几何形状是递归的数据结构，我们必须先提取出单独的线段，才能进行进一步处理。

    运行这个程序会生成一个包含多个点的长列表，这些点构成了定义两国边界的各个线段：

    .. code-block:: text

        % python calcBorderLength.py
        [[(100.08132200000006, 20.348840999999936),
        (100.08943199999999, 20.347217999999941)],
        [(100.08943199999999, 20.347217999999941),
        (100.0913700000001, 20.348606000000075)], ...]

    每个线段由一对点组成——在此例中，你会注意到每个线段只有两个点——而且如果你仔细观察，你会发现每个线段的起点与上一个线段的终点相同。总共有 459 条线段定义了泰国和缅甸之间的边界——即 459 对点，我们可以计算它们之间的大地测量距离。

    .. note::

        请记住，大地测量距离是指在地球表面上测量的距离。

    接下来，让我们看看如何使用 pyproj 计算任意两点之间的大地测量距离。我们首先创建一个 Geod 实例：

    .. code-block:: python

        geod = pyproj.Geod(ellps='WGS84')

    Geod 是 pyproj 类，用于执行大地测量计算。请注意，我们需要提供描述地球形状的基准数据。设置好 Geod 实例后，我们可以通过调用 geod.inv() 来计算任意两点之间的大地测量距离，这是“大地测量反向变换”方法：

    .. code-block:: python

        angle1,angle2,distance = geod.inv(long1, lat1, long2, lat2)

    `angle1` 将是从第一个点到第二个点的角度，单位是十进制度， `angle2` 将是从第二个点回到第一个点的角度（同样是以度为单位），而 `distance` 将是两点之间的大圆距离，单位是米。

    利用这一点，我们可以遍历所有线段，计算每一对点之间的距离，并将所有的距离相加，以得到总的边界长度：

    .. code-block:: python

        geod = pyproj.Geod(ellps='WGS84')

        totLength = 0.0

        for segment in segments:
            for i in range(len(segment)-1):
                pt1 = segment[i]
                pt2 = segment[i+1]
        
                long1,lat1 = pt1
                long2,lat2 = pt2
                
                angle1,angle2,distance = geod.inv(long1, lat1, long2, lat2)
                totLength += distance

    运行完毕后， `totLength` 将是边界的总长度，单位是米。

    将所有这些代码组合在一起，我们得到一个完整的 Python 程序，它读取 `border.shp` shapefile，计算并显示共同边界的总长度：

    .. code-block:: python

        # calcBorderLength.py
        
        import os.path
        from osgeo import ogr
        import pyproj

        def getLineSegmentsFromGeometry(geometry):
            segments = []
            if geometry.GetPointCount() > 0:
                segment = []
                for i in range(geometry.GetPointCount()):
                    segment.append(geometry.GetPoint_2D(i))
                    segments.append(segment)
            for i in range(geometry.GetGeometryCount()):
                subGeometry = geometry.GetGeometryRef(i)
                segments.extend(getLineSegmentsFromGeometry(subGeometry))
            return segments

        filename = os.path.join("common-border", "border.shp")
        shapefile = ogr.Open(filename)
        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(0)
        geometry = feature.GetGeometryRef()
        segments = getLineSegmentsFromGeometry(geometry)

        geod = pyproj.Geod(ellps='WGS84')

        totLength = 0.0
        for segment in segments:
            for i in range(len(segment)-1):
                pt1 = segment[i]
                pt2 = segment[i+1]

                long1,lat1 = pt1
                long2,lat2 = pt2

                angle1,angle2,distance = geod.inv(long1, lat1, long2, lat2)
                totLength += distance

        print "Total border length = %0.2f km" % (totLength/1000)

    运行这个程序会告诉我们泰国-缅甸边界的总计算长度：

        % python calcBorderLength.py
        Total border length = 1730.55 km

    在这个程序中，我们假设 shapefile 使用的是地理坐标，采用 WGS84 椭球，并且只包含一个特征。接下来，让我们扩展我们的程序，使其能够处理任何提供的投影和基准，并同时处理 shapefile 中的所有特征，而不仅仅是第一个。这将使我们的程序更具灵活性，并允许它处理任何任意的 shapefile，而不仅仅是我们之前创建的 `common-border` shapefile。

    首先，我们处理投影和基准。我们可以像本章前面处理 LULC 和 lkA02020 shapefiles 时那样，在处理前更改 shapefile 的投影和基准。这也可以工作，但这会要求我们创建一个临时的 shapefile 来进行长度计算，这样效率不高。相反，我们直接使用 pyproj 将 shapefile 重新投影到地理坐标系，如果需要的话。我们可以通过查询 shapefile 的空间参考来实现这一点：

    .. code-block:: python

        shapefile = ogr.Open(filename)
        layer = shapefile.GetLayer(0)
        spatialRef = layer.GetSpatialRef()
        if spatialRef == None:
            print "Shapefile has no spatial reference, using WGS84."
            spatialRef = osr.SpatialReference()
            spatialRef.SetWellKnownGeogCS('WGS84')

    一旦我们得到了空间参考，我们可以检查该参考是否是投影坐标系统，如果是，我们使用 pyproj 将投影坐标转换回纬度/经度值，如下所示：

    .. code-block:: python

        if spatialRef.IsProjected():
            # Convert projected coordinates back to lat/long values.
            srcProj = pyproj.Proj(spatialRef.ExportToProj4())
            dstProj = pyproj.Proj(proj='longlat', ellps='WGS84',
            datum='WGS84')
        ...
        long,lat = pyproj.transform(srcProj, dstProj, x, y)

    使用这个方法，我们可以重写程序，以接受任何投影和基准的数据。同时，我们会修改程序，以计算文件中每个特征的总长度，而不仅仅是第一个特征，还会接受命令行传入的 shapefile 文件名。最后，我们将增加一些错误检查。我们将这个新程序命名为 `calcFeatureLengths.py`。

    我们将首先复制之前使用的 `getLineSegmentsFromGeometry()` 函数：

    .. code-block:: python

        import sys
        from osgeo import ogr, osr
        import pyproj

        def getLineSegmentsFromGeometry(geometry):
            segments = []
            if geometry.GetPointCount() > 0:
                segment = []
                for i in range(geometry.GetPointCount()):
                    segment.append(geometry.GetPoint_2D(i))
                    segments.append(segment)
            for i in range(geometry.GetGeometryCount()):
                subGeometry = geometry.GetGeometryRef(i)
                segments.extend(getLineSegmentsFromGeometry(subGeometry))
            
            return segments

    接下来，我们从命令行获取要打开的 shapefile 名称：

    .. code-block:: python

        if len(sys.argv) != 2:
            print "Usage: calcFeatureLengths.py <shapefile>"
            sys.exit(1)
        
        filename = sys.argv[1]

    然后，我们打开 shapefile 并获取其空间参考，使用之前写的代码：

    .. code-block:: python

    shapefile = ogr.Open(filename)
    layer = shapefile.GetLayer(0)
    spatialRef = layer.GetSpatialRef()
    if spatialRef == None:
    print "Shapefile lacks a spatial reference, using WGS84."
    spatialRef = osr.SpatialReference()
    spatialRef.SetWellKnownGeogCS('WGS84')

    接下来，我们获取源投影和目标投影，依然使用之前写的代码。注意，我们只在使用投影坐标系时需要这么做：

    .. code-block:: python

        if spatialRef.IsProjected():
            srcProj = pyproj.Proj(spatialRef.ExportToProj4())
            dstProj = pyproj.Proj(proj='longlat', ellps='WGS84',
                                    datum='WGS84')

    现在我们已经准备好开始处理 shapefile 的特征了：

    .. code-block:: python

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)

    一旦获取了特征，我们就可以借用之前的代码，计算该特征线段的总长度：

    .. code-block:: python

        geometry = feature.GetGeometryRef()
        segments = getLineSegmentsFromGeometry(geometry)

        geod = pyproj.Geod(ellps='WGS84')

        totLength = 0.0
        for segment in segments:
            for j in range(len(segment)-1):


                pt1 = segment[j]
                pt2 = segment[j+1]

    唯一的区别是，如果我们使用的是投影坐标系统，我们需要将坐标转换回 WGS84：

    .. code-block:: python

        if spatialRef.IsProjected():
            long1,lat1 = pyproj.transform(srcProj,
                                            dstProj,
                                            long1, lat1)
            long2,lat2 = pyproj.transform(srcProj,
                                            dstProj,
                                            long2, lat2)

    然后我们就可以像之前的例子那样使用 pyproj 计算两点之间的距离。不过这次我们将它包装在 try...except 语句中，以确保计算失败时程序不会崩溃：

    .. code-block:: python
        
        try:
            angle1,angle2,distance = geod.inv(long1, lat1,
                                                long2, lat2)
        except ValueError:
            print "Unable to calculate distance from " \
                    + "%0.4f,%0.4f to %0.4f,%0.4f" \
                    % (long1, lat1, long2, lat2)
            distance = 0.0
        totLength += distance

    .. note::

        `geod.inv()` 调用可能会抛出 `ValueError`，例如当两点位于无法计算角度的位置时——比如当两点位于地球的极点。

    最后，我们可以打印出特征的总长度，单位为千米：

    .. code-block:: python

        print "Total length of feature %d is %0.2f km" \
            % (i, totLength/1000)

    该程序可以在任何 shapefile 上运行，而不管其投影和基准。例如，你可以通过运行它来计算世界各国边界的长度，方法是使用世界边界数据集：

    .. code-block:: text

        % python calcFeatureLengths.py TM_WORLD_BORDERS-0.3.shp
        Total length of feature 0 is 127.28 km
        Total length of feature 1 is 7264.69 km
        Total length of feature 2 is 2514.76 km
        Total length of feature 3 is 968.86 km
        Total length of feature 4 is 1158.92 km
        Total length of feature 5 is 6549.53 km
        Total length of feature 6 is 119.27 km

    这个程序是一个将几何坐标转换为距离的示例。让我们来看看反向计算：如何使用距离来计算新的几何坐标。

.. tab:: 英文

    To solve this problem, we will make use of the common-border/border.shp shapefile we created earlier. This shapefile contains a single feature, which is a LineString defining the border between the two countries. Let's start by taking a look at the individual line segments that make up this feature's geometry:

    .. code-block:: python

        import os.path
        from osgeo import ogr

        def getLineSegmentsFromGeometry(geometry):
            segments = []
            if geometry.GetPointCount() > 0:
                segment = []
                for i in range(geometry.GetPointCount()):
                    segment.append(geometry.GetPoint_2D(i))
                    segments.append(segment)
            for i in range(geometry.GetGeometryCount()):
                subGeometry = geometry.GetGeometryRef(i)
                segments.extend(getLineSegmentsFromGeometry(subGeometry))
            return segments

        filename = os.path.join("common-border", "border.shp")
        shapefile = ogr.Open(filename)
        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(0)
        geometry = feature.GetGeometryRef()

        segments = getLineSegmentsFromGeometry(geometry)

        print segments

    .. note::

        Don't forget to change the os.path.join() statement to match the location of your border.shp shapefile.

    Note that we use a recursive function, *getLineSegmentsFromGeometry()*, to pull the individual coordinates for each line segment out of the geometry. Because geometries are recursive data structures, we have to pull out the individual line segments before we can work with them.

    Running this program produces a long list of points that make up the various line segments defining the border between these two countries:

    .. code-block:: text

        % python calcBorderLength.py
        [[(100.08132200000006, 20.348840999999936),
        (100.08943199999999, 20.347217999999941)],
        [(100.08943199999999, 20.347217999999941),
        (100.0913700000001, 20.348606000000075)], ...]

    Each line segment consists of a list of points—in this case, you'll notice that each segment has only two points—and if you look closely you will notice that each segment starts at the same point as the previous segment ended. There are a total of 459 segments defining the border between Thailand and Myanmar—that is, 459 point pairs that we can calculate the geodetic distance for.

    .. note::

        Remember that a geodetic distance is a distance measured on the surface of the Earth.

    Let's see how we can use pyproj to calculate the geodetic distance between any two points. We first create a Geod instance:

    .. code-block:: python

        geod = pyproj.Geod(ellps='WGS84')

    Geod is the pyproj class that performs geodetic calculations. Note that we have to provide it with details of the datum used to describe the shape of the Earth. Once our Geod instance has been set up, we can calculate the geodetic distance between any two points by calling geod.inv(), the "inverse geodetic transformation" method:

    .. code-block:: python

        angle1,angle2,distance = geod.inv(long1, lat1, long2, lat2)

    angle1 will be the angle from the first point to the second, measured in decimal degrees, angle2 will be the angle from the second point back to the first (again in degrees), and distance will be the Great Circle distance between the two points, in meters.

    Using this, we can iterate over the line segments, calculate the distance from one point to another, and total up all the distances to obtain the total length of the border:

    .. code-block:: python

        geod = pyproj.Geod(ellps='WGS84')

        totLength = 0.0

        for segment in segments:
            for i in range(len(segment)-1):
                pt1 = segment[i]
                pt2 = segment[i+1]
        
                long1,lat1 = pt1
                long2,lat2 = pt2
                
                angle1,angle2,distance = geod.inv(long1, lat1, long2, lat2)
                totLength += distance

    Upon completion, totLength will be the total length of the border, in meters. 
    
    Putting all this together, we end up with a complete Python program to read the border.shp shapefile, calculate and then display the total length of the common border:

    .. code-block:: python

        # calcBorderLength.py
        
        import os.path
        from osgeo import ogr
        import pyproj

        def getLineSegmentsFromGeometry(geometry):
            segments = []
            if geometry.GetPointCount() > 0:
                segment = []
                for i in range(geometry.GetPointCount()):
                    segment.append(geometry.GetPoint_2D(i))
                    segments.append(segment)
            for i in range(geometry.GetGeometryCount()):
                subGeometry = geometry.GetGeometryRef(i)
                segments.extend(getLineSegmentsFromGeometry(subGeometry))
            return segments

        filename = os.path.join("common-border", "border.shp")
        shapefile = ogr.Open(filename)
        layer = shapefile.GetLayer(0)
        feature = layer.GetFeature(0)
        geometry = feature.GetGeometryRef()
        segments = getLineSegmentsFromGeometry(geometry)

        geod = pyproj.Geod(ellps='WGS84')

        totLength = 0.0
        for segment in segments:
            for i in range(len(segment)-1):
                pt1 = segment[i]
                pt2 = segment[i+1]

                long1,lat1 = pt1
                long2,lat2 = pt2

                angle1,angle2,distance = geod.inv(long1, lat1, long2, lat2)
                totLength += distance

        print "Total border length = %0.2f km" % (totLength/1000)

    Running this program tells us the total calculated length of the Thai-Myanmar border:

        % python calcBorderLength.py
        Total border length = 1730.55 km

    In this program, we have assumed that the shapefile is in geographic coordinates using the WGS84 ellipsoid, and only contains a single feature. Let's extend our program to deal with any supplied projection and datum, and at the same time process all the features in the shapefile rather than just the first. This will make our program more flexible, and allow it to work with any arbitrary shapefile rather than just the common-border shapefile we created earlier.

    Let's deal with the projection and datum first. We could change the projection and datum for our shapefile before we process it, just as we did with the LULC and lkA02020 shapefiles earlier in this chapter. That would work, but it would require us to create a temporary shapefile just to calculate the length, which isn't very efficient. Instead, let's make use of pyproj directly to reproject the shapefile back into geographic coordinates if necessary. We can do this by querying the shapefile's spatial reference:

    .. code-block:: python

        shapefile = ogr.Open(filename)
        layer = shapefile.GetLayer(0)
        spatialRef = layer.GetSpatialRef()
        if spatialRef == None:
            print "Shapefile has no spatial reference, using WGS84."
            spatialRef = osr.SpatialReference()
            spatialRef.SetWellKnownGeogCS('WGS84')

    Once we have the spatial reference, we can see if the spatial reference is projected, and if so use pyproj to turn the projected coordinates back into lat/long values again, like this:

    .. code-block:: python

        if spatialRef.IsProjected():
            # Convert projected coordinates back to lat/long values.
            srcProj = pyproj.Proj(spatialRef.ExportToProj4())
            dstProj = pyproj.Proj(proj='longlat', ellps='WGS84',
            datum='WGS84')
        ...
        long,lat = pyproj.transform(srcProj, dstProj, x, y)

    Using this, we can rewrite our program to accept data using any projection
    and datum. At the same time, we'll change it to calculate the overall length of
    every feature in the file, rather than just the first, and also to accept the name
    of the shapefile from the command line. Finally, we'll add some error-checking.
    Let's call our new program calcFeatureLengths.py.

    We'll start by copying the getLineSegmentsFromGeometry() function we used earlier:

    .. code-block:: python

        import sys
        from osgeo import ogr, osr
        import pyproj

        def getLineSegmentsFromGeometry(geometry):
            segments = []
            if geometry.GetPointCount() > 0:
                segment = []
                for i in range(geometry.GetPointCount()):
                    segment.append(geometry.GetPoint_2D(i))
                    segments.append(segment)
            for i in range(geometry.GetGeometryCount()):
                subGeometry = geometry.GetGeometryRef(i)
                segments.extend(
                    getLineSegmentsFromGeometry(subGeometry))
            
            return segments

    Next, we'll get the name of the shapefile to open from the command line:

    .. code-block:: python

        if len(sys.argv) != 2:
            print "Usage: calcFeatureLengths.py <shapefile>"
            sys.exit(1)
        
        filename = sys.argv[1]

    We'll then open the shapefile and obtain its spatial reference, using the code we wrote earlier:

    .. code-block:: python

    shapefile = ogr.Open(filename)
    layer = shapefile.GetLayer(0)
    spatialRef = layer.GetSpatialRef()
    if spatialRef == None:
    print "Shapefile lacks a spatial reference, using WGS84."
    spatialRef = osr.SpatialReference()
    spatialRef.SetWellKnownGeogCS('WGS84')

    We'll then get the source and destination projections, again using the code we wrote earlier. Note that we only need to do this if we're using projected coordinates:

    .. code-block:: python

        if spatialRef.IsProjected():
            srcProj = pyproj.Proj(spatialRef.ExportToProj4())
            dstProj = pyproj.Proj(proj='longlat', ellps='WGS84',
                                  datum='WGS84')

    We are now ready to start processing the shapefile's features:

    .. code-block:: python

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)

    Now that we have the feature, we can borrow the code we used earlier to calculate the total length of that feature's line segments:

    .. code-block:: python

        geometry = feature.GetGeometryRef()
        segments = getLineSegmentsFromGeometry(geometry)

        geod = pyproj.Geod(ellps='WGS84')

        totLength = 0.0
        for segment in segments:
            for j in range(len(segment)-1):
                pt1 = segment[j]
                pt2 = segment[j+1]

                long1,lat1 = pt1
                long2,lat2 = pt2
    
    The only difference is that we need to transform the coordinates back to WGS84 if we are using a projected coordinate system:

    .. code-block:: python

        if spatialRef.IsProjected():
            long1,lat1 = pyproj.transform(srcProj,
                                            dstProj,
                                            long1, lat1)
            long2,lat2 = pyproj.transform(srcProj,
                                            dstProj,
                                            long2, lat2)

    We can then use pyproj to calculate the distance between the two points, as we did in our earlier example. This time, though, we'll wrap it in a try...except statement so that any failure to calculate the distance won't crash the program:

    .. code-block:: python
        
        try:
            angle1,angle2,distance = geod.inv(long1, lat1,
                                              long2, lat2)
        except ValueError:
            print "Unable to calculate distance from " \
                    + "%0.4f,%0.4f to %0.4f,%0.4f" \
                    % (long1, lat1, long2, lat2)
            distance = 0.0
        totLength += distance

    .. note::

        The geod.inv() call can raise a ValueError if the two coordinates are in a place where an angle can't be calculated—for example if the two points are at the poles. 

    And finally, we can print out the feature's total length, in kilometers:

    .. code-block:: python

        print "Total length of feature %d is %0.2f km" \
            % (i, totLength/1000)

    This program can be run over any shapefile, regardless of the projection and datum. For example, you could use it to calculate the border length for every country in the world by running it over the World Borders Dataset:

    .. code-block:: text

        % python calcFeatureLengths.py TM_WORLD_BORDERS-0.3.shp
        Total length of feature 0 is 127.28 km
        Total length of feature 1 is 7264.69 km
        Total length of feature 2 is 2514.76 km
        Total length of feature 3 is 968.86 km
        Total length of feature 4 is 1158.92 km
        Total length of feature 5 is 6549.53 km
        Total length of feature 6 is 119.27 km

    This program is an example of converting geometry coordinates into distances. Let's take a look at the inverse calculation: using distances to calculate new geometry coordinates.

任务 - 找到加利福尼亚州索肖尼以西 132.7 公里的一个点
-------------------------------------------------------------------------
Task – find a point 132.7 kilometers west of Soshone, California

.. tab:: 中文

    使用我们之前下载的 `NationalFile_XXX.txt` 文件，我们可以找到位于拉斯维加斯东部的加利福尼亚州一个小镇 Shoshone 的纬度和经度：

    .. code-block:: python

        f = file("NationalFile_XXXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
            if chunks[1] == "Shoshone" and \
                chunks[2] == "Populated Place" and \
                chunks[3] == "CA":
                latitude = float(chunks[9])
                longitude = float(chunks[10])

    给定这些坐标，我们可以使用 pyproj 计算在给定距离和给定角度下，某个点的新坐标：

    .. code-block:: python

        geod = pyproj.Geod(ellps="WGS84")
        newLong,newLat,invAngle = geod.fwd(latitude, longitude,
                                            angle, distance)

    在这个任务中，我们知道目标距离，并且知道我们希望的角度是“正西”。pyproj 使用的是方位角，角度是从北方顺时针测量的。因此，正西对应的角度是 270 度。

    将这些信息结合起来，我们可以计算出目标点的坐标：

    .. code-block:: python

        # findShoshone.py

        import pyproj

        distance = 132.7 * 1000
        angle = 270.0

        f = file("NationalFile_XXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
            if chunks[1] == "Shoshone" and \
                chunks[2] == "Populated Place" and \
                chunks[3] == "CA":
                    latitude = float(chunks[9])
                    longitude = float(chunks[10])

                    geod = pyproj.Geod(ellps='WGS84')
                    newLong,newLat,invAngle = geod.fwd(longitude,
                                                        latitude,
                                                        angle, distance)
                    print "Shoshone is at %0.4f,%0.4f" % (latitude,
                                                            longitude)
                    print "The point %0.2f km west of Shoshone " \
                        % (distance/1000.0) \
                        + "is at %0.4f, %0.4f" % (newLat, newLong)
        f.close()

    运行此程序将给出我们想要的答案：

    .. code-block:: text

        % python findShoshone.py
        Shoshone is at 35.9730,-116.2711
        The point 132.70 km west of Shoshone is at 35.9640,
        -117.7423

.. tab:: 英文

    Using the NationalFile_XXX.txt file we downloaded earlier, it is possible to find the latitude and longitude of Shoshone, a small town in California east of Las Vegas:

    .. code-block:: python

        f = file("NationalFile_XXXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
            if chunks[1] == "Shoshone" and \
                chunks[2] == "Populated Place" and \
                chunks[3] == "CA":
                latitude = float(chunks[9])
                longitude = float(chunks[10])

    Given this coordinate, we can use pyproj to calculate the coordinate of a point a given distance away, at a given angle:

    .. code-block:: python

        geod = pyproj.Geod(ellps="WGS84")
        newLong,newLat,invAngle = geod.fwd(latitude, longitude,
                                            angle, distance)

    For this task, we are given the desired distance and we know that the angle we want is "due west". pyproj uses azimuth angles, which are measured clockwise from North. Thus, due west would correspond to an angle of 270 degrees.

    Putting all this together, we can calculate the coordinates of the desired point:

    .. code-block:: python

        # findShoshone.py

        import pyproj

        distance = 132.7 * 1000
        angle = 270.0

        f = file("NationalFile_XXX.txt", "r")
        for line in f.readlines():
            chunks = line.rstrip().split("|")
            if chunks[1] == "Shoshone" and \
                chunks[2] == "Populated Place" and \
                chunks[3] == "CA":
                    latitude = float(chunks[9])
                    longitude = float(chunks[10])

                    geod = pyproj.Geod(ellps='WGS84')
                    newLong,newLat,invAngle = geod.fwd(longitude,
                                                        latitude,
                                                        angle, distance)
                    print "Shoshone is at %0.4f,%0.4f" % (latitude,
                                                            longitude)
                    print "The point %0.2f km west of Shoshone " \
                        % (distance/1000.0) \
                        + "is at %0.4f, %0.4f" % (newLat, newLong)
        f.close()

    Running this program gives us the answer we want:

    .. code-block:: text

        % python findShoshone.py
        Shoshone is at 35.9730,-116.2711
        The point 132.70 km west of Shoshone is at 35.9640,
        -117.7423
