处理投影
============================================

Dealing with projections

.. tab:: 中文

    One of the challenges of working with geospatial data is that geodetic locations (points on the Earth's surface) are mapped into a two-dimensional Cartesian plane using a cartographic projection. We looked at projections in the previous chapter: whenever you have some geospatial data, you need to know which projection that data uses. You also need to know the datum (model of the Earth's shape) assumed by the data.

    A common challenge when dealing with geospatial data is that you have to convert data from one projection/datum to another. Fortunately, there is a Python library pyproj which makes this task easy.

.. tab:: 英文

    One of the challenges of working with geospatial data is that geodetic locations (points on the Earth's surface) are mapped into a two-dimensional Cartesian plane using a cartographic projection. We looked at projections in the previous chapter: whenever you have some geospatial data, you need to know which projection that data uses. You also need to know the datum (model of the Earth's shape) assumed by the data.

    A common challenge when dealing with geospatial data is that you have to convert data from one projection/datum to another. Fortunately, there is a Python library pyproj which makes this task easy.

pyproj
----------

.. tab:: 中文

    *pyproj* is a Python "wrapper" around another library called *PROJ.4*. "PROJ.4" is an abbreviation for Version 4 of the *PROJ* library. PROJ was originally written by the US Geological Survey for dealing with map projections, and has been widely used in geospatial software for many years. The *pyproj* library makes it possible to access the functionality of PROJ.4 from within your Python programs.

.. tab:: 英文

    *pyproj* is a Python "wrapper" around another library called *PROJ.4*. "PROJ.4" is an abbreviation for Version 4 of the *PROJ* library. PROJ was originally written by the US Geological Survey for dealing with map projections, and has been widely used in geospatial software for many years. The *pyproj* library makes it possible to access the functionality of PROJ.4 from within your Python programs.

pyproj

设计
----------

Design

.. tab:: 中文

    The pyproj library consists of the following pieces:

    .. image:: ./img/77-0.png
       :align: center
       :class: with-border
       :scale: 70

    *pyproj* consists of just two classes: **Proj** and **Geod**. *Proj* converts from longitude and latitude values to native map (x, y) coordinates, and vice versa. *Geod* performs various Great Circle distance and angle calculations. Both are built on top of the *PROJ.4* library. Let's take a closer look at these two classes.

.. tab:: 英文

    The pyproj library consists of the following pieces:

    .. image:: ./img/77-0.png
       :align: center
       :class: with-border
       :scale: 70

    *pyproj* consists of just two classes: **Proj** and **Geod**. *Proj* converts from longitude and latitude values to native map (x, y) coordinates, and vice versa. *Geod* performs various Great Circle distance and angle calculations. Both are built on top of the *PROJ.4* library. Let's take a closer look at these two classes.

Proj
~~~~~~~~~~

Proj

.. tab:: 中文

    Proj is a cartographic transformation class, allowing you to convert geographic
    coordinates (that is, latitude and longitude values) into cartographic coordinates
    (x, y values, by default in meters) and vice versa.

    When you create a new Proj instance, you specify the projection, datum,
    and other values used to describe how the projection is to be done. For example,
    to use the Transverse Mercator projection and the WGS84 ellipsoid, you would
    do the following::

        projection = pyproj.Proj(proj='tmerc', ellps='WGS84')

    Once you have created a Proj instance, you can use it to convert a latitude and
    longitude to an (x, y) coordinate using the given projection. You can also use it to
    do an inverse projection—that is, converting from an (x, y) coordinate back into a
    latitude and longitude value again.

    The helpful transform() function can be used to directly convert coordinates from
    one projection to another. You simply provide the starting coordinates, the Proj
    object that describes the starting coordinates' projection, and the desired ending
    projection. This can be very useful when converting coordinates, either singly or
    en masse.

.. tab:: 英文

    Proj is a cartographic transformation class, allowing you to convert geographic
    coordinates (that is, latitude and longitude values) into cartographic coordinates
    (x, y values, by default in meters) and vice versa.

    When you create a new Proj instance, you specify the projection, datum,
    and other values used to describe how the projection is to be done. For example,
    to use the Transverse Mercator projection and the WGS84 ellipsoid, you would
    do the following::

        projection = pyproj.Proj(proj='tmerc', ellps='WGS84')

    Once you have created a Proj instance, you can use it to convert a latitude and
    longitude to an (x, y) coordinate using the given projection. You can also use it to
    do an inverse projection—that is, converting from an (x, y) coordinate back into a
    latitude and longitude value again.

    The helpful transform() function can be used to directly convert coordinates from
    one projection to another. You simply provide the starting coordinates, the Proj
    object that describes the starting coordinates' projection, and the desired ending
    projection. This can be very useful when converting coordinates, either singly or
    en masse.


Geod
~~~~~~~~~~

Geod

.. tab:: 中文

    *Geod* is a geodetic computation class, which allows you to perform various Great
    Circle calculations. We looked at Great Circle calculations earlier, when considering
    how to accurately calculate the distance between two points on the Earth's surface.
    The Geod class, however, can do more than this:

    - The *fwd()* method takes a starting point, an azimuth (angular direction) and a distance, and returns the ending point and the back azimuth (the angle from the end point back to the start point again):

    .. image:: ./img/78-0.png
       :class: with-border
       :align: center
       :scale: 70

    - The *inv()* method takes two coordinates and returns the forward and back azimuth as well as the distance between them:

    .. image:: ./img/78-1.png
       :class: with-border
       :align: center
       :scale: 70

    - The *npts()* method calculates the coordinates of a number of points spaced equidistantly along a geodesic line running from the start to the end point:

    .. image:: ./img/79-0.png
       :class: with-border
       :align: center
       :scale: 70

    When you create a new *Geod* object, you specify the ellipsoid to use when performing the geodetic calculations. The ellipsoid can be selected from a number of predefined ellipsoids, or you can enter the parameters for the ellipsoid (equatorial radius, polar radius, and so on) directly.

.. tab:: 英文

    *Geod* is a geodetic computation class, which allows you to perform various Great
    Circle calculations. We looked at Great Circle calculations earlier, when considering
    how to accurately calculate the distance between two points on the Earth's surface.
    The Geod class, however, can do more than this:

    - The *fwd()* method takes a starting point, an azimuth (angular direction) and a distance, and returns the ending point and the back azimuth (the angle from the end point back to the start point again):

    .. image:: ./img/78-0.png
       :class: with-border
       :align: center
       :scale: 70

    - The *inv()* method takes two coordinates and returns the forward and back azimuth as well as the distance between them:

    .. image:: ./img/78-1.png
       :class: with-border
       :align: center
       :scale: 70

    - The *npts()* method calculates the coordinates of a number of points spaced equidistantly along a geodesic line running from the start to the end point:

    .. image:: ./img/79-0.png
       :class: with-border
       :align: center
       :scale: 70

    When you create a new *Geod* object, you specify the ellipsoid to use when performing the geodetic calculations. The ellipsoid can be selected from a number of predefined ellipsoids, or you can enter the parameters for the ellipsoid (equatorial radius, polar radius, and so on) directly.


示例代码
--------------------

Example code

.. tab:: 中文

    The following example starts with a location specified using UTM zone 17 coordinates.
    Using two Proj objects to define the UTM Zone 17 and lat/long projections,
    it translates this location's coordinates into latitude and longitude values:

    .. code-block:: python
            
        import pyproj

        UTM_X = 565718.5235
        UTM_Y = 3980998.9244
        
        srcProj = pyproj.Proj(proj="utm", zone="11", ellps="clrk66", units="m")
        dstProj = pyproj.Proj(proj="longlat", ellps="WGS84", datum="WGS84")

        long,lat = pyproj.transform(srcProj, dstProj, UTM_X, UTM_Y)

        print "UTM zone 11 coordinate (%0.4f, %0.4f) = %0.4f, %0.4f" \
        % (UTM_X, UTM_Y, lat, long)

    Continuing on with this example, let's take the calculated lat/long values and, using
    a Geod object, calculate another point 10 kilometers northeast of that location:

    .. code-block:: python

        angle    = 315 # 315 degrees = northeast.
        distance = 10000

        geod = pyproj.Geod(ellps="WGS84")
        long2,lat2,invAngle = geod.fwd(long, lat, angle, distance)
        
        print "%0.4f, %0.4f is 10km northeast of %0.4f, %0.4f" \
        % (lat2, long2, lat, long)

.. tab:: 英文

    The following example starts with a location specified using UTM zone 17 coordinates.
    Using two Proj objects to define the UTM Zone 17 and lat/long projections,
    it translates this location's coordinates into latitude and longitude values:

    .. code-block:: python
            
        import pyproj

        UTM_X = 565718.5235
        UTM_Y = 3980998.9244
        
        srcProj = pyproj.Proj(proj="utm", zone="11", ellps="clrk66", units="m")
        dstProj = pyproj.Proj(proj="longlat", ellps="WGS84", datum="WGS84")

        long,lat = pyproj.transform(srcProj, dstProj, UTM_X, UTM_Y)

        print "UTM zone 11 coordinate (%0.4f, %0.4f) = %0.4f, %0.4f" \
        % (UTM_X, UTM_Y, lat, long)

    Continuing on with this example, let's take the calculated lat/long values and, using
    a Geod object, calculate another point 10 kilometers northeast of that location:

    .. code-block:: python

        angle    = 315 # 315 degrees = northeast.
        distance = 10000

        geod = pyproj.Geod(ellps="WGS84")
        long2,lat2,invAngle = geod.fwd(long, lat, angle, distance)
        
        print "%0.4f, %0.4f is 10km northeast of %0.4f, %0.4f" \
        % (lat2, long2, lat, long)


文档
--------------------

Documentation

.. tab:: 中文

    The documentation available on the pyproj website, and in the docs directory
    provided with the source code, is excellent as far as it goes. It describes how to
    use the various classes and methods, what they do and what parameters are
    required. However, the documentation is rather sparse when it comes to the
    parameters used when creating a new Proj object. As the documentation says:

        *A Proj class instance is initialized with proj map projection control parameter key/
        value pairs. The key/value pairs can either be passed in a dictionary, or as keyword
        arguments, or as a proj4 string (compatible with the proj command).*

    The documentation does provide a link to a website listing a number of standard
    map projections and their associated parameters, but understanding what these
    parameters mean generally requires you to delve into the PROJ documentation
    itself. The documentation for PROJ is dense and confusing, even more so because
    the main manual is written for PROJ Version 3, with addendums for later versions.
    Attempting to make sense of all this can be quite challenging.

    Fortunately, in most cases you won't need to refer to the PROJ documentation at
    all. When working with geospatial data using GDAL or OGR, you can easily extract
    the projection as a "proj4 string" which can be passed directly to the Proj initializer.
    If you want to hardwire the projection, you can generally choose a projection and
    ellipsoid using the *proj="..."* and *ellps="..."* parameters, respectively. If you
    want to do more than this, though, you will need to refer to the PROJ documentation
    for more details.

    .. note::

        To find out more about PROJ, and to read the original documentation, you can find everything you need at: http://trac.osgeo.org/proj

.. tab:: 英文

    The documentation available on the pyproj website, and in the docs directory
    provided with the source code, is excellent as far as it goes. It describes how to
    use the various classes and methods, what they do and what parameters are
    required. However, the documentation is rather sparse when it comes to the
    parameters used when creating a new Proj object. As the documentation says:

        *A Proj class instance is initialized with proj map projection control parameter key/
        value pairs. The key/value pairs can either be passed in a dictionary, or as keyword
        arguments, or as a proj4 string (compatible with the proj command).*

    The documentation does provide a link to a website listing a number of standard
    map projections and their associated parameters, but understanding what these
    parameters mean generally requires you to delve into the PROJ documentation
    itself. The documentation for PROJ is dense and confusing, even more so because
    the main manual is written for PROJ Version 3, with addendums for later versions.
    Attempting to make sense of all this can be quite challenging.

    Fortunately, in most cases you won't need to refer to the PROJ documentation at
    all. When working with geospatial data using GDAL or OGR, you can easily extract
    the projection as a "proj4 string" which can be passed directly to the Proj initializer.
    If you want to hardwire the projection, you can generally choose a projection and
    ellipsoid using the *proj="..."* and *ellps="..."* parameters, respectively. If you
    want to do more than this, though, you will need to refer to the PROJ documentation
    for more details.

    .. note::

        To find out more about PROJ, and to read the original documentation, you can find everything you need at: http://trac.osgeo.org/proj


可用性
--------------------

Availability

.. tab:: 中文

    Prebuild versions of pyproj are available for MS Windows, with source code
    distributions for other platforms. The main web page for pyproj can be found at:

    http://code.google.com/p/pyproj

    How you go about installing it depends on which operating system you are running.

    .. note::

        Make sure that you install Version 4.8.0 or later of the PROJ framework,
        and Version 1.9.2 or later of the pyproj library. These versions are
        required to follow the examples in this book.

    - **MS Windows**
        For computers running MS Windows, installation is easy: just go to the
        downloads page at the website mentioned earlier and and choose the
        appropriate installer for your version of Python. The installer includes
        everything you need, including the PROJ framework.
    - **Linux**
        For computers running Linux, you have to download and install the PROJ
        framework separately, before installing pyproj. For Linux machines, you
        can generally obtain PROJ.4 as an RPM or source tarball which you can
        then compile yourself. Once this has been done, you can download the
        pyproj source code from the above website, and compile and install it
        in the usual way::

            python setup.py build
            python setup.py install

    - **Macintosh**
        If your computer runs Mac OS X, you will also have to download and install
        PROJ separately. You can install a compiled version of the PROJ framework
        either as part of a "GDAL Complete" installation, or by just installing the
        PROJ framework by itself. Either are available at:
        
        http://www.kyngchaos.com/software/frameworks

    Once you have installed PROJ.4, you will have to download and build your own
    copy of the pyproj library. Before you can compile pyproj, you will need to have
    Apple's developer tools installed. Doing this is a two-step process:

    1. Download and install the latest version of XCode. XCode is available for free from the App store, or if you are running an older version of OS X you can download it from:

        https://developer.apple.com/xcode

    2. Run XCode, and choose the Preferences command. Within the Downloads tab, click on the Install button beside the Command Line Tools item:

    .. image:: ./img/82-0.png
       :scale: 70
       :class: with-border
       :align: center

    This installs the command-line tools you will need to compile *pyproj*.

    Once you have the developer tools installed, download the source code to pyproj
    from the website mentioned earlier. Then open up a Terminal window and cd into
    the main source code directory, then type the following commands:

        python setup.py build
        sudo python.setup.py install

    .. note::

        The sudo command allows pyproj to install itself inside your Python installation's site-packages directory. You'll be asked to enter your password before this is done.

    Once this has finished, you can check that it worked by running the Python
    interpreter and typing the following command::

        import pyproj

    The Python prompt should reappear without any error messages being shown.

.. tab:: 英文

    Prebuild versions of pyproj are available for MS Windows, with source code
    distributions for other platforms. The main web page for pyproj can be found at:

    http://code.google.com/p/pyproj

    How you go about installing it depends on which operating system you are running.

    .. note::

        Make sure that you install Version 4.8.0 or later of the PROJ framework,
        and Version 1.9.2 or later of the pyproj library. These versions are
        required to follow the examples in this book.

    - **MS Windows**
        For computers running MS Windows, installation is easy: just go to the
        downloads page at the website mentioned earlier and and choose the
        appropriate installer for your version of Python. The installer includes
        everything you need, including the PROJ framework.
    - **Linux**
        For computers running Linux, you have to download and install the PROJ
        framework separately, before installing pyproj. For Linux machines, you
        can generally obtain PROJ.4 as an RPM or source tarball which you can
        then compile yourself. Once this has been done, you can download the
        pyproj source code from the above website, and compile and install it
        in the usual way::

            python setup.py build
            python setup.py install

    - **Macintosh**
        If your computer runs Mac OS X, you will also have to download and install
        PROJ separately. You can install a compiled version of the PROJ framework
        either as part of a "GDAL Complete" installation, or by just installing the
        PROJ framework by itself. Either are available at:
        
        http://www.kyngchaos.com/software/frameworks

    Once you have installed PROJ.4, you will have to download and build your own
    copy of the pyproj library. Before you can compile pyproj, you will need to have
    Apple's developer tools installed. Doing this is a two-step process:

    1. Download and install the latest version of XCode. XCode is available for free from the App store, or if you are running an older version of OS X you can download it from:

        https://developer.apple.com/xcode

    2. Run XCode, and choose the Preferences command. Within the Downloads tab, click on the Install button beside the Command Line Tools item:

    .. image:: ./img/82-0.png
       :scale: 70
       :class: with-border
       :align: center

    This installs the command-line tools you will need to compile *pyproj*.

    Once you have the developer tools installed, download the source code to pyproj
    from the website mentioned earlier. Then open up a Terminal window and cd into
    the main source code directory, then type the following commands:

        python setup.py build
        sudo python.setup.py install

    .. note::

        The sudo command allows pyproj to install itself inside your Python installation's site-packages directory. You'll be asked to enter your password before this is done.

    Once this has finished, you can check that it worked by running the Python
    interpreter and typing the following command::

        import pyproj

    The Python prompt should reappear without any error messages being shown.
