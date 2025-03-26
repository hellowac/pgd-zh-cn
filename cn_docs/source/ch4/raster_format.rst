栅格格式地理空间数据源
============================================

Sources of geospatial data in raster format

.. tab:: 中文

    One of the most enthralling aspects of programs such as Google Earth is the ability
    to "see" the Earth as you appear to fly above it. This is achieved by displaying
    satellite and aerial photographs carefully stitched together to provide the illusion
    that you are viewing the Earth's surface from above.

    While writing your own version of Google Earth would be an almost impossible
    task, it is possible to obtain free satellite imagery in the form of raster format
    geospatial data, which you can then use in your own geospatial applications.

    Raster data is not just limited to images of the Earth's surface however; other useful
    information can be found in raster format—for example, **digital elevation maps
    (DEM)** contain the height of each point on the Earth's surface, which can then be
    used to calculate the elevation of any desired point. DEM data can also be used to
    generate two-dimensional images that represent different heights using different
    shades or colors, or to simulate the shading effect of hills using a technique called
    **shaded relief imagery**.

    In this section, we will look at an extremely comprehensive source of satellite
    imagery, the raster-format data available on the Natural Earth site, and some
    freely-available sources of digital elevation data.

.. tab:: 英文

    One of the most enthralling aspects of programs such as Google Earth is the ability
    to "see" the Earth as you appear to fly above it. This is achieved by displaying
    satellite and aerial photographs carefully stitched together to provide the illusion
    that you are viewing the Earth's surface from above.

    While writing your own version of Google Earth would be an almost impossible
    task, it is possible to obtain free satellite imagery in the form of raster format
    geospatial data, which you can then use in your own geospatial applications.

    Raster data is not just limited to images of the Earth's surface however; other useful
    information can be found in raster format—for example, **digital elevation maps
    (DEM)** contain the height of each point on the Earth's surface, which can then be
    used to calculate the elevation of any desired point. DEM data can also be used to
    generate two-dimensional images that represent different heights using different
    shades or colors, or to simulate the shading effect of hills using a technique called
    **shaded relief imagery**.

    In this section, we will look at an extremely comprehensive source of satellite
    imagery, the raster-format data available on the Natural Earth site, and some
    freely-available sources of digital elevation data.

Landsat
----------

Landsat

.. tab:: 中文

    Landsat is an ongoing effort to collect images of the Earth's surface. The name is
    derived from land and satellite. A group of dedicated satellites have been continuously
    gathering images since 1972. Landsat imagery includes black and white, traditional
    red/green/blue (RGB) color images, as well as infrared and thermal imaging. The
    color images are typically at a resolution of 30 meters per pixel, while the black and
    white images from Landsat 7 are at a resolution of 15 meters per pixel.

    The following screenshot shows color-corrected Landsat satellite imagery for the city
    of Rotorua, New Zealand. The city itself is on the southern (bottom) edge of a lake:

    .. image:: ./img/111-0.png
       :class: with-border
       :align: center
       :scale: 50

.. tab:: 英文

    Landsat is an ongoing effort to collect images of the Earth's surface. The name is
    derived from land and satellite. A group of dedicated satellites have been continuously
    gathering images since 1972. Landsat imagery includes black and white, traditional
    red/green/blue (RGB) color images, as well as infrared and thermal imaging. The
    color images are typically at a resolution of 30 meters per pixel, while the black and
    white images from Landsat 7 are at a resolution of 15 meters per pixel.

    The following screenshot shows color-corrected Landsat satellite imagery for the city
    of Rotorua, New Zealand. The city itself is on the southern (bottom) edge of a lake:

    .. image:: ./img/111-0.png
       :class: with-border
       :align: center
       :scale: 50


数据格式
~~~~~~~~~~~~~

Data format

.. tab:: 中文

    Landsat images are typically available in the form of GeoTIFF files. GeoTIFF is a
    geospatially tagged TIFF image file format, allowing images to be georeferenced
    onto the Earth's surface. Most GIS software and tools, including GDAL, are able
    to read GeoTIFF formatted files.

    Because the images come directly from a satellite, the files you can download
    typically store separate bands of data in separate files. Depending on the satellite
    the data came from, there can be up to eight different bands of data—for example,
    Landsat 7 generates separate red, green, and blue bands, as well as three different
    infrared bands, a thermal band, and a high-resolution "panchromatic" (black-and-
    white) band.

    To understand how this works, let's take a closer look at the process required to create
    the preceding screenshot. The raw satellite data consists of eight separate GeoTIFF
    files, one for each band. Band 1 contains the blue color data, band 2 contains the green
    color data, and band 3 contains the red color data. These separate files can then be
    combined using GDAL to produce a single color image as follows:

    .. image:: ./img/112-0.png
       :class: with-border
       :align: center
       :scale: 90

    Another complication with the Landsat data is that the images produced by the
    satellites are distorted by various factors, including the ellipsoid shape of the
    Earth, the elevation of the terrain being photographed, and the orientation of the
    satellite as the image is taken. The raw data is therefore not a completely accurate
    representation of the features being photographed. Fortunately a process known
    as **orthorectification** can be used to correct these distortions. In most cases,
    orthorectified versions of the satellite images can be downloaded directly.

.. tab:: 英文

    Landsat images are typically available in the form of GeoTIFF files. GeoTIFF is a
    geospatially tagged TIFF image file format, allowing images to be georeferenced
    onto the Earth's surface. Most GIS software and tools, including GDAL, are able
    to read GeoTIFF formatted files.

    Because the images come directly from a satellite, the files you can download
    typically store separate bands of data in separate files. Depending on the satellite
    the data came from, there can be up to eight different bands of data—for example,
    Landsat 7 generates separate red, green, and blue bands, as well as three different
    infrared bands, a thermal band, and a high-resolution "panchromatic" (black-and-
    white) band.

    To understand how this works, let's take a closer look at the process required to create
    the preceding screenshot. The raw satellite data consists of eight separate GeoTIFF
    files, one for each band. Band 1 contains the blue color data, band 2 contains the green
    color data, and band 3 contains the red color data. These separate files can then be
    combined using GDAL to produce a single color image as follows:

    .. image:: ./img/112-0.png
       :class: with-border
       :align: center
       :scale: 90

    Another complication with the Landsat data is that the images produced by the
    satellites are distorted by various factors, including the ellipsoid shape of the
    Earth, the elevation of the terrain being photographed, and the orientation of the
    satellite as the image is taken. The raw data is therefore not a completely accurate
    representation of the features being photographed. Fortunately a process known
    as **orthorectification** can be used to correct these distortions. In most cases,
    orthorectified versions of the satellite images can be downloaded directly.


获取 Landsat 影像
~~~~~~~~~~~~~~~~~~~~~

Obtaining Landsat imagery

.. tab:: 中文

    The easiest way to access Landsat imagery is to make use of the University of
    Maryland's Global Land Cover Facility website:

    http://glcf.umiacs.umd.edu/data/landsat

    Click on the **Download via Search and Preview Tool** link, and then click on **Map
    Search**. Select **ETM+** from the **Landsat Imagery** list, and if you zoom in on the
    desired part of the Earth you will see the areas covered by various Landsat images:

    .. image:: ./img/113-0.png
       :class: with-border
       :align: center
       :scale: 70

    If you choose the selection tool ( |inline-image1| ), you will be able to click on a desired area, then
    select **Preview & Download** to choose the image to download.

    Alternatively, if you know the path and row number of the desired area of the earth,
    you can directly access the files via FTP. The path and row number (as well as the
    **world reference system (WRS)** used by the data) can be found on the **Preview &
    Download** page:

    .. image:: ./img/114-0.png
       :class: with-border
       :align: center
       :scale: 50

    If you want to download the image files via FTP, the main FTP site is at:

    ftp://ftp.glcf.umd.edu/glcf/Landsat

    The directories and files have complex names which include the WRS, the path and
    row number, the satellite number, the date at which the image was taken, and the
    band number. For example, a file named p091r089_7t20001123_z55_nn10.tif.
    gz refers to path 091 and row 089, which happens to be the portion of Tasmania
    highlighted in the preceding screenshot. The 7 refers to the number of the Landsat
    satellite that took the image, and 20001123 is a datestamp indicating when the image
    was taken. The final part of the filename, nn10, tells us that the file is for band 1.

    By interpreting the filename in this way, you can download the correct files, and
    match the files against the desired bands. For more information on what all these
    different satellites and bands mean, refer to the documentation links in the upper
    right-hand corner of the Global Land Cover Facility website:

    http://glcf.umiacs.umd.edu/data/landsat

.. tab:: 英文

    The easiest way to access Landsat imagery is to make use of the University of
    Maryland's Global Land Cover Facility website:

    http://glcf.umiacs.umd.edu/data/landsat

    Click on the **Download via Search and Preview Tool** link, and then click on **Map
    Search**. Select **ETM+** from the **Landsat Imagery** list, and if you zoom in on the
    desired part of the Earth you will see the areas covered by various Landsat images:

    .. image:: ./img/113-0.png
       :class: with-border
       :align: center
       :scale: 70

    If you choose the selection tool ( |inline-image1| ), you will be able to click on a desired area, then
    select **Preview & Download** to choose the image to download.

    Alternatively, if you know the path and row number of the desired area of the earth,
    you can directly access the files via FTP. The path and row number (as well as the
    **world reference system (WRS)** used by the data) can be found on the **Preview &
    Download** page:

    .. image:: ./img/114-0.png
       :class: with-border
       :align: center
       :scale: 50

    If you want to download the image files via FTP, the main FTP site is at:

    ftp://ftp.glcf.umd.edu/glcf/Landsat

    The directories and files have complex names which include the WRS, the path and
    row number, the satellite number, the date at which the image was taken, and the
    band number. For example, a file named p091r089_7t20001123_z55_nn10.tif.
    gz refers to path 091 and row 089, which happens to be the portion of Tasmania
    highlighted in the preceding screenshot. The 7 refers to the number of the Landsat
    satellite that took the image, and 20001123 is a datestamp indicating when the image
    was taken. The final part of the filename, nn10, tells us that the file is for band 1.

    By interpreting the filename in this way, you can download the correct files, and
    match the files against the desired bands. For more information on what all these
    different satellites and bands mean, refer to the documentation links in the upper
    right-hand corner of the Global Land Cover Facility website:

    http://glcf.umiacs.umd.edu/data/landsat


Natural Earth
--------------------

Natural Earth

.. tab:: 中文

    In addition to providing vector map data, the Natural Earth website (http://www.naturalearthdata.com) makes available five different types of raster maps at both 1:10 million and 1:50 million scale:

    - The rather esoterically-named **Cross-Blended Hypsometric Tints** provide visualizations where the color is selected based on both elevation and climate. These images are then often combined with shaded relief images to make a realistic-looking view of the Earth's surface.
    - **Natural Earth 1** and **Natural Earth 2** are more idealized views of the Earth's surface, using a light palette and softly-blended colors, providing an excellent backdrop for drawing your own geospatial data.
    - The **Ocean Bottom** dataset uses a combination of shaded relief imagery and depth-based coloring to provide a visualization of the ocean floor.
    - The **Shaded Relief** imagery uses greyscale to "shade" the surface of the Earth based on high-resolution elevation data.

    One additional raster dataset is available that provides bathymetry (underwater depth) visualizations at 1:50 million scale. The following screenshot is an example of the bathymetry data for the oceans surrounding New Zealand:

    .. image:: ./img/116-0.png
       :class: with-border
       :align: center
       :scale: 50

.. tab:: 英文

    In addition to providing vector map data, the Natural Earth website (http://www.naturalearthdata.com) makes available five different types of raster maps at both 1:10 million and 1:50 million scale:

    - The rather esoterically-named **Cross-Blended Hypsometric Tints** provide visualizations where the color is selected based on both elevation and climate. These images are then often combined with shaded relief images to make a realistic-looking view of the Earth's surface.
    - **Natural Earth 1** and **Natural Earth 2** are more idealized views of the Earth's surface, using a light palette and softly-blended colors, providing an excellent backdrop for drawing your own geospatial data.
    - The **Ocean Bottom** dataset uses a combination of shaded relief imagery and depth-based coloring to provide a visualization of the ocean floor.
    - The **Shaded Relief** imagery uses greyscale to "shade" the surface of the Earth based on high-resolution elevation data.

    One additional raster dataset is available that provides bathymetry (underwater depth) visualizations at 1:50 million scale. The following screenshot is an example of the bathymetry data for the oceans surrounding New Zealand:

    .. image:: ./img/116-0.png
       :class: with-border
       :align: center
       :scale: 50


数据格式
~~~~~~~~~~~~~

Data format

.. tab:: 中文

    Most of the raster-format data on the Natural Earth site is in the standard TIFF image
    format. The one exception is the bathymetry data, which is provided in the form of
    a layered Adobe Photoshop file with differing shades of blue associated with each
    depth band.

    In all cases, the raster data is in geographic (latitude/longitude) projection, and
    uses the standard WGS84 datum, making it easy to translate between latitude
    and longitude coordinates and pixel coordinates within the raster image.

.. tab:: 英文

    Most of the raster-format data on the Natural Earth site is in the standard TIFF image
    format. The one exception is the bathymetry data, which is provided in the form of
    a layered Adobe Photoshop file with differing shades of blue associated with each
    depth band.

    In all cases, the raster data is in geographic (latitude/longitude) projection, and
    uses the standard WGS84 datum, making it easy to translate between latitude
    and longitude coordinates and pixel coordinates within the raster image.


获取和使用 Natural Earth 栅格数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using Natural Earth raster data

.. tab:: 中文

    As with the vector data, the raster-format data on the Natural Earth site is easy to
    download; simply go to the site and follow the **Get the Data** link to download the
    raster-format data. You can choose to download the data at either 1:10 million scale,
    or 1:50 million scale, and you can also choose to download the large or small size
    of each file.

    Once you have downloaded the TIFF format data, you can open the file in an image
    editor, or use a standard command-line utility such as gdal_translate to manipulate
    the image. For the bathymetry data, you can open the file directly in Adobe Photoshop,
    or use a cheaper alternative such as the GIMP or Flying Meat's Acorn. Each depth band
    is a separate layer in the file, and by default is associated with a specific shade of blue.
    You can choose different colors if you prefer, and can select which layers to show or
    hide. When you are finished, you can then flatten the image and save it as a TIFF file
    for use in your programs.

.. tab:: 英文

    As with the vector data, the raster-format data on the Natural Earth site is easy to
    download; simply go to the site and follow the **Get the Data** link to download the
    raster-format data. You can choose to download the data at either 1:10 million scale,
    or 1:50 million scale, and you can also choose to download the large or small size
    of each file.

    Once you have downloaded the TIFF format data, you can open the file in an image
    editor, or use a standard command-line utility such as gdal_translate to manipulate
    the image. For the bathymetry data, you can open the file directly in Adobe Photoshop,
    or use a cheaper alternative such as the GIMP or Flying Meat's Acorn. Each depth band
    is a separate layer in the file, and by default is associated with a specific shade of blue.
    You can choose different colors if you prefer, and can select which layers to show or
    hide. When you are finished, you can then flatten the image and save it as a TIFF file
    for use in your programs.


全球陆地一公里基准高程 (GLOBE)
--------------------------------------------------

Global Land One-kilometer Base Elevation (GLOBE)

.. tab:: 中文

    GLOBE is an international effort to produce high-quality, medium-resolution digital
    elevation (DEM) data for the entire world. The result is a set of freely-available DEM
    files, which can be used for many types of geospatial analysis and development.

    The following screenshot shows GLOBE DEM data for northern Chile, converted to
    a grayscale image:

    .. image:: ./img/117-0.png
       :class: with-border
       :align: center
       :scale: 50

.. tab:: 英文

    GLOBE is an international effort to produce high-quality, medium-resolution digital
    elevation (DEM) data for the entire world. The result is a set of freely-available DEM
    files, which can be used for many types of geospatial analysis and development.

    The following screenshot shows GLOBE DEM data for northern Chile, converted to
    a grayscale image:

    .. image:: ./img/117-0.png
       :class: with-border
       :align: center
       :scale: 50


数据格式
~~~~~~~~~~~~~

Data format

.. tab:: 中文

    Like all DEM data, GLOBE uses raster values to represent the elevation at a given point
    on the Earth's surface. In the case of GLOBE, this data consists of 32-bit signed integers
    representing the height above (or below) sea level, in meters. Each cell or "pixel" within
    the raster data represents the elevation of a square on the Earth's surface which is 30
    arc-seconds of longitude wide, and 30 arc-seconds of latitude high:

    .. image:: ./img/118-0.png
       :class: with-border
       :align: center
       :scale: 50

    Note that 30 arc-seconds equals approximately 0.00833 degrees of latitude
    or longitude, which equates to a square roughly one kilometer wide and
    one kilometer high.

    The raw GLOBE data is simply a long list of 32-bit integers in big-endian format,
    where the cells are read left-to-right and then top-to-bottom, like this:

    .. csv-table::

        "x=0, y=0", "x=1, y=0", "…", "x=10800, y=0"
        "x=0, y=1", "x=1, y=1", "…", "x=10800, y=1"
        "…", "…", "…", "…"
        "x=0, y=6000", "x=1, y=6000", "…", "x=10800,y=6000"

    A separate header (.hdr) file provides more detailed information about the DEM data, including the width and height and its georeferenced location. Tools such as GDAL are able to read the raw data as long as the header file is provided.

.. tab:: 英文

    Like all DEM data, GLOBE uses raster values to represent the elevation at a given point
    on the Earth's surface. In the case of GLOBE, this data consists of 32-bit signed integers
    representing the height above (or below) sea level, in meters. Each cell or "pixel" within
    the raster data represents the elevation of a square on the Earth's surface which is 30
    arc-seconds of longitude wide, and 30 arc-seconds of latitude high:

    .. image:: ./img/118-0.png
       :class: with-border
       :align: center
       :scale: 50

    Note that 30 arc-seconds equals approximately 0.00833 degrees of latitude
    or longitude, which equates to a square roughly one kilometer wide and
    one kilometer high.

    The raw GLOBE data is simply a long list of 32-bit integers in big-endian format,
    where the cells are read left-to-right and then top-to-bottom, like this:

    .. csv-table::

        "x=0, y=0", "x=1, y=0", "…", "x=10800, y=0"
        "x=0, y=1", "x=1, y=1", "…", "x=10800, y=1"
        "…", "…", "…", "…"
        "x=0, y=6000", "x=1, y=6000", "…", "x=10800,y=6000"

    A separate header (.hdr) file provides more detailed information about the DEM data, including the width and height and its georeferenced location. Tools such as GDAL are able to read the raw data as long as the header file is provided.

获取和使用 GLOBE 数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using GLOBE data

.. tab:: 中文

    The main website for the GLOBE project can be found at:

    http://www.ngdc.noaa.gov/mgg/topo/globe.html

    For detailed documentation of the GLOBE data, you can follow the **Get Data
    Online** link to download precalculated sets of data or to choose a given area
    to download DEM data for.

    If you download one of the premade tiles, you will need to also download the
    associated .hdr file so that the data can be georeferenced and processed using
    GDAL. If you choose a custom area to download, a suitable *.hdr* file will be
    created for you—just make sure you choose an export type of **ESRI ArcView**
    so that the header is created in the format expected by GDAL.

    If you download a premade tile, the header files can be quite hard to find.
    Suitable header files in ESRI format can be downloaded from:

    http://www.ngdc.noaa.gov/mgg/topo/elev/esri/hdr

    Once you have downloaded the data, simply place the raw DEM file into the
    same directory as the .hdr file. You can then open the file directly using GDAL,
    like this::

        import osgeo.gdal
        dataset = osgeo.gdal.Open("j10g.bil")

    The dataset will consist of a single band of raster data, which you can then translate,
    read or process using the GDAL library and related tools.

    .. note::

        To see an example of using GDAL to process DEM data, please refer to the GDAL section in Chapter 3, Python Libraries for Geospatial Development.

.. tab:: 英文

    The main website for the GLOBE project can be found at:

    http://www.ngdc.noaa.gov/mgg/topo/globe.html

    For detailed documentation of the GLOBE data, you can follow the **Get Data
    Online** link to download precalculated sets of data or to choose a given area
    to download DEM data for.

    If you download one of the premade tiles, you will need to also download the
    associated .hdr file so that the data can be georeferenced and processed using
    GDAL. If you choose a custom area to download, a suitable *.hdr* file will be
    created for you—just make sure you choose an export type of **ESRI ArcView**
    so that the header is created in the format expected by GDAL.

    If you download a premade tile, the header files can be quite hard to find.
    Suitable header files in ESRI format can be downloaded from:

    http://www.ngdc.noaa.gov/mgg/topo/elev/esri/hdr

    Once you have downloaded the data, simply place the raw DEM file into the
    same directory as the .hdr file. You can then open the file directly using GDAL,
    like this::

        import osgeo.gdal
        dataset = osgeo.gdal.Open("j10g.bil")

    The dataset will consist of a single band of raster data, which you can then translate,
    read or process using the GDAL library and related tools.

    .. note::

        To see an example of using GDAL to process DEM data, please refer to the GDAL section in Chapter 3, Python Libraries for Geospatial Development.


国家高程数据集 (NED)
----------------------------------------

National Elevation Dataset (NED)

.. tab:: 中文

    The National Elevation Dataset (NED) is a high-resolution digital elevation dataset
    provided by the US Geological Survey. It covers the Continental United States, Alaska,
    Hawaii, and other US territories. Most of the United States is covered by elevation
    data at 30 meters/pixel or 10 meters/pixel resolution, with selected areas available
    at 3 meters/pixel. Alaska is generally only available at 60 meters/pixel resolution.

    The following shaded relief screenshot was generated using NED elevation data for
    the Marin Headlands, San Francisco:

    .. image:: ./img/120-0.png
       :class: with-border
       :align: center
       :scale: 50

.. tab:: 英文

    The National Elevation Dataset (NED) is a high-resolution digital elevation dataset
    provided by the US Geological Survey. It covers the Continental United States, Alaska,
    Hawaii, and other US territories. Most of the United States is covered by elevation
    data at 30 meters/pixel or 10 meters/pixel resolution, with selected areas available
    at 3 meters/pixel. Alaska is generally only available at 60 meters/pixel resolution.

    The following shaded relief screenshot was generated using NED elevation data for
    the Marin Headlands, San Francisco:

    .. image:: ./img/120-0.png
       :class: with-border
       :align: center
       :scale: 50


数据格式
~~~~~~~~~~~~~

Data format

.. tab:: 中文

    The NED data can be downloaded in various formats including GeoTIFF and
    ArcGRID, both of which can be processed using GDAL.

    As with other DEM data, each "pixel" in the raster image represents the height of a
    given area on the Earth's surface. For NED data, the height is in meters above or below
    a reference height known as the North American Vertical Datum of 1988. This roughly
    equates to the height above or below sea level, allowing for tidal and other variations.

.. tab:: 英文

    The NED data can be downloaded in various formats including GeoTIFF and
    ArcGRID, both of which can be processed using GDAL.

    As with other DEM data, each "pixel" in the raster image represents the height of a
    given area on the Earth's surface. For NED data, the height is in meters above or below
    a reference height known as the North American Vertical Datum of 1988. This roughly
    equates to the height above or below sea level, allowing for tidal and other variations.


获取和使用 NED 数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using NED data

.. tab:: 中文

    The main website for the National Elevation Dataset can be found at:

    http://ned.usgs.gov

    This site describes the NED dataset; to download the data you'll have to use the
    National Map Viewer, which is available at:

    http://viewer.nationalmap.gov/viewer/

    To use the viewer, zoom in to the area you want, and then click on the **Download
    Data** option at the top of the page:

    .. image:: ./img/120-1.png
       :class: with-border
       :align: center

    Click on this option to download by the current map extent, and select **Elevation** as the data you want to download. You can choose from a variety of data formats; GeoTIFF is a good option to use. A window then appears to show the various sets of elevation data you can download:

    .. image:: ./img/121-0.png
       :class: with-border
       :align: center
       :scale: 90

    Downloading data from the National Map Viewer is a bit like buying something online: you add the desired item to your "cart", then you "checkout" your order and enter your e-mail address. Once you "place your order", you'll be sent an e-mail with links to where you can download the data you need.

    Unfortunately, the National Map Viewer is quite slow to make the data available;
    expect to spend several minutes waiting for the data to start downloading.

    You will receive a compressed *.zip* format file containing the data you want,
    along with a large number of metadata files and documentation about the
    National Elevation Dataset.

    .. note::

        Note that you might need to rename the files to remove the backslashes before you can open them; GDAL can get confused by filenames with backslashes.

    Once you have downloaded the desired GeoTIFF files, you can open them in GDAL just as you would open any other raster dataset::

        import osgeo.gdal
        dataset = osgeo.gdal.Open("dem.tif")

    Finally, if you are working with DEM data you might like to check out the gdaldem utility, which is included as part of the GDAL download. This program makes it easy to view and manipulate DEM raster data. The preceding shaded relief screenshot was created using this utility, like this::

        gdaldem hillshade dem.tif image.tiff

.. tab:: 英文

    The main website for the National Elevation Dataset can be found at:

    http://ned.usgs.gov

    This site describes the NED dataset; to download the data you'll have to use the
    National Map Viewer, which is available at:

    http://viewer.nationalmap.gov/viewer/

    To use the viewer, zoom in to the area you want, and then click on the **Download
    Data** option at the top of the page:

    .. image:: ./img/120-1.png
       :class: with-border
       :align: center

    Click on this option to download by the current map extent, and select **Elevation** as the data you want to download. You can choose from a variety of data formats; GeoTIFF is a good option to use. A window then appears to show the various sets of elevation data you can download:

    .. image:: ./img/121-0.png
       :class: with-border
       :align: center
       :scale: 90

    Downloading data from the National Map Viewer is a bit like buying something online: you add the desired item to your "cart", then you "checkout" your order and enter your e-mail address. Once you "place your order", you'll be sent an e-mail with links to where you can download the data you need.

    Unfortunately, the National Map Viewer is quite slow to make the data available;
    expect to spend several minutes waiting for the data to start downloading.

    You will receive a compressed *.zip* format file containing the data you want,
    along with a large number of metadata files and documentation about the
    National Elevation Dataset.

    .. note::

        Note that you might need to rename the files to remove the backslashes before you can open them; GDAL can get confused by filenames with backslashes.

    Once you have downloaded the desired GeoTIFF files, you can open them in GDAL just as you would open any other raster dataset::

        import osgeo.gdal
        dataset = osgeo.gdal.Open("dem.tif")

    Finally, if you are working with DEM data you might like to check out the gdaldem utility, which is included as part of the GDAL download. This program makes it easy to view and manipulate DEM raster data. The preceding shaded relief screenshot was created using this utility, like this::

        gdaldem hillshade dem.tif image.tiff


.. |inline-image1| image:: ./img/113-1.png
