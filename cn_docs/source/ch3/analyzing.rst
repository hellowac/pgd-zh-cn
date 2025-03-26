分析和处理地理空间数据
============================================

Analyzing and manipulating geospatial data

.. tab:: 中文

    Because geospatial data works with geometrical features such as points, lines, and polygons, you often need to perform various calculations using these geometrical features. Fortunately, there are some very powerful tools for doing exactly this. For reasons we will describe shortly, the library of choice for performing this type of computational geometry in Python is **Shapely**.

.. tab:: 英文

    Because geospatial data works with geometrical features such as points, lines, and polygons, you often need to perform various calculations using these geometrical features. Fortunately, there are some very powerful tools for doing exactly this. For reasons we will describe shortly, the library of choice for performing this type of computational geometry in Python is **Shapely**.


Shapely
----------

Shapely

.. tab:: 中文

    Shapely is a Python package for the manipulation and analysis of two-dimensional geospatial geometries. Shapely is based on the GEOS library, which implements a wide range of geospatial data manipulations in C++. GEOS is itself based on a library called the Java Topology Suite, which provides the same functionality for Java programmers. Shapely provides a Pythonic interface to GEOS which makes it easy to use these manipulations directly from your Python programs.

.. tab:: 英文

    Shapely is a Python package for the manipulation and analysis of two-dimensional geospatial geometries. Shapely is based on the GEOS library, which implements a wide range of geospatial data manipulations in C++. GEOS is itself based on a library called the Java Topology Suite, which provides the same functionality for Java programmers. Shapely provides a Pythonic interface to GEOS which makes it easy to use these manipulations directly from your Python programs.


设计
----------

Design

.. tab:: 中文

    The Shapely library is organized as follows:

    .. image:: ./img/84-0.png
       :scale: 70
       :class: with-border
       :align: center

    All of Shapely's functionality is built on top of GEOS. Indeed, Shapely requires GEOS
    to be installed before it can run.

    Shapely itself consists of eight major classes, representing different types of
    geometrical shapes:

    - The **Point** class represents a single point in space. Points can be two-dimensional (x, y), or three-dimensional (x, y, z).
    - The **LineString** class represents a sequence of points joined together to form a line. LineStrings can be *simple* (no crossing line segments) or *complex* (where two line segments within the LineString cross).
    - The **LinearRing** class represents a line string which finishes at the starting point. The line segments within a LinearRing cannot cross or touch.
    - The **Polygon** class represents a filled area, optionally with one or more "holes" inside it.
    - The **MultiPoint** class represents a collection of Points.
    - The **MultiLineString** class represents a collection of LineStrings.
    - The **MultiPolygon** class represents a collection of Polygons.
    - The **GeometryCollection** class represents a collection of any combination of Points, LineStrings, LinearRings, and Polygons.

    As well as being able to represent these various types of geometries, Shapely provides
    a number of methods and attributes for manipulating and analyzing these geometries.
    For example, the LineString class provides a *length* attribute that equals the length
    of all the line segments that make up the LineString, and a *crosses()* method that
    returns true if two LineStrings cross. Other methods allow you to calculate the
    intersection of two polygons, dilate or erode geometries, simplify a geometry, calculate
    the distance between two geometries, and build a polygon that encloses all the points
    within a given list of geometries (called the *convex_hull* attribute).

    Note that Shapely is a *spatial* manipulation library rather than a geospatial manipulation
    library. It has no concept of geographical coordinates. Instead, it assumes that the
    geospatial data has been projected onto a two-dimensional Cartesian plane before it
    is manipulated, and the results can then be converted back into geographic coordinates
    if desired.

.. tab:: 英文

    The Shapely library is organized as follows:

    .. image:: ./img/84-0.png
       :scale: 70
       :class: with-border
       :align: center

    All of Shapely's functionality is built on top of GEOS. Indeed, Shapely requires GEOS
    to be installed before it can run.

    Shapely itself consists of eight major classes, representing different types of
    geometrical shapes:

    - The **Point** class represents a single point in space. Points can be two-dimensional (x, y), or three-dimensional (x, y, z).
    - The **LineString** class represents a sequence of points joined together to form a line. LineStrings can be *simple* (no crossing line segments) or *complex* (where two line segments within the LineString cross).
    - The **LinearRing** class represents a line string which finishes at the starting point. The line segments within a LinearRing cannot cross or touch.
    - The **Polygon** class represents a filled area, optionally with one or more "holes" inside it.
    - The **MultiPoint** class represents a collection of Points.
    - The **MultiLineString** class represents a collection of LineStrings.
    - The **MultiPolygon** class represents a collection of Polygons.
    - The **GeometryCollection** class represents a collection of any combination of Points, LineStrings, LinearRings, and Polygons.

    As well as being able to represent these various types of geometries, Shapely provides
    a number of methods and attributes for manipulating and analyzing these geometries.
    For example, the LineString class provides a *length* attribute that equals the length
    of all the line segments that make up the LineString, and a *crosses()* method that
    returns true if two LineStrings cross. Other methods allow you to calculate the
    intersection of two polygons, dilate or erode geometries, simplify a geometry, calculate
    the distance between two geometries, and build a polygon that encloses all the points
    within a given list of geometries (called the *convex_hull* attribute).

    Note that Shapely is a *spatial* manipulation library rather than a geospatial manipulation
    library. It has no concept of geographical coordinates. Instead, it assumes that the
    geospatial data has been projected onto a two-dimensional Cartesian plane before it
    is manipulated, and the results can then be converted back into geographic coordinates
    if desired.


示例代码
----------

Example code

.. tab:: 中文

    The following program creates two Shapely geometry objects, a circle and a square, and calculates their intersection:

    .. image:: ./img/85-0.png
       :scale: 70
       :class: with-border
       :align: center

    The intersection will be a polygon in the shape of a quarter circle , as indicated by the dark grey portion of the preceding image:

    .. code-block:: python

        import shapely.geometry

        pt = shapely.geometry.Point(0, 0)
        circle = pt.buffer(1.0)

        square = shapely.geometry.Polygon([(0, 0), (1, 0),
                                        (1, 1), (0, 1),
                                        (0, 0)])

        intersect = circle.intersection(square)

        for x,y in intersect.exterior.coords:
            print x,y

    Notice how the circle is constructed by taking a Point geometry and using the *buffer()* method to create a Polygon representing the outline of a circle.

.. tab:: 英文

    The following program creates two Shapely geometry objects, a circle and a square, and calculates their intersection:

    .. image:: ./img/85-0.png
       :scale: 70
       :class: with-border
       :align: center

    The intersection will be a polygon in the shape of a quarter circle , as indicated by the dark grey portion of the preceding image:

    .. code-block:: python

        import shapely.geometry

        pt = shapely.geometry.Point(0, 0)
        circle = pt.buffer(1.0)

        square = shapely.geometry.Polygon([(0, 0), (1, 0),
                                        (1, 1), (0, 1),
                                        (0, 0)])

        intersect = circle.intersection(square)

        for x,y in intersect.exterior.coords:
            print x,y

    Notice how the circle is constructed by taking a Point geometry and using the *buffer()* method to create a Polygon representing the outline of a circle.


文档
--------------------

Documentation

.. tab:: 中文

    Shapely comes with excellent documentation, with detailed descriptions, extended
    code samples, and many illustrations that clearly show how the various classes,
    methods, and attributes work.

    The Shapely documentation is entirely self-contained; there is no need to refer to
    the GEOS documentation, or to the Java Topology Suite it is based on, unless you
    particularly want to see how things are done in these libraries. The only exception
    is that you may need to refer to the GEOS documentation if you are compiling
    GEOS from source and are having problems getting it to work.

.. tab:: 英文

    Shapely comes with excellent documentation, with detailed descriptions, extended
    code samples, and many illustrations that clearly show how the various classes,
    methods, and attributes work.

    The Shapely documentation is entirely self-contained; there is no need to refer to
    the GEOS documentation, or to the Java Topology Suite it is based on, unless you
    particularly want to see how things are done in these libraries. The only exception
    is that you may need to refer to the GEOS documentation if you are compiling
    GEOS from source and are having problems getting it to work.


可用性
--------------------

Availability

.. tab:: 中文

    Shapely will run on all major operating systems, including MS Windows, Mac OS X,
    and Linux. Shapely's main website can be found at:

    http://pypi.python.org/pypi/Shapely

    The website has everything you need, including the documentation and downloads for
    the Shapely library, in both source code form and prebuilt binaries for MS Windows.

    If you are installing Shapely on a Windows computer, the prebuilt binaries include
    the GEOS library built-in. Otherwise, you will be responsible for installing GEOS
    before you can use Shapely.

    .. note::

        Make sure that you install Shapely Version 1.2 or later; you will need this version to work through the examples in this book.

    The GEOS library's website is at:

    http://trac.osgeo.org/geos

    To install GEOS in a Unix-based computer, you can either download the source code
    from the GEOS website and compile it yourself, or you can install a suitable RPM
    or APT package which includes GEOS. If you are running Mac OS X, you can either
    try to download and build GEOS yourself, or you can install the prebuild GEOS
    framework, which is available from the following website:

    http://www.kyngchaos.com/software/frameworks

    .. note::

        If you've installed the "GDAL Complete" package from the above website, you'll already have GEOS installed on your Mac OS X computer.

    After installing GEOS, you need to download, compile, and install the Shapely
    library. This can be slightly tricky on a Mac OS X computer, so you may find the
    following blog post useful:

    http://tumblr.pauladamsmith.com/post/17663153373

.. tab:: 英文

    Shapely will run on all major operating systems, including MS Windows, Mac OS X,
    and Linux. Shapely's main website can be found at:

    http://pypi.python.org/pypi/Shapely

    The website has everything you need, including the documentation and downloads for
    the Shapely library, in both source code form and prebuilt binaries for MS Windows.

    If you are installing Shapely on a Windows computer, the prebuilt binaries include
    the GEOS library built-in. Otherwise, you will be responsible for installing GEOS
    before you can use Shapely.

    .. note::

        Make sure that you install Shapely Version 1.2 or later; you will need this version to work through the examples in this book.

    The GEOS library's website is at:

    http://trac.osgeo.org/geos

    To install GEOS in a Unix-based computer, you can either download the source code
    from the GEOS website and compile it yourself, or you can install a suitable RPM
    or APT package which includes GEOS. If you are running Mac OS X, you can either
    try to download and build GEOS yourself, or you can install the prebuild GEOS
    framework, which is available from the following website:

    http://www.kyngchaos.com/software/frameworks

    .. note::

        If you've installed the "GDAL Complete" package from the above website, you'll already have GEOS installed on your Mac OS X computer.

    After installing GEOS, you need to download, compile, and install the Shapely
    library. This can be slightly tricky on a Mac OS X computer, so you may find the
    following blog post useful:

    http://tumblr.pauladamsmith.com/post/17663153373
