矢量格式的地理空间数据来源
============================================

Sources of geospatial data in vector format

.. tab:: 中文

    Vector-based geospatial data represents physical features as collections of points, lines, and polygons. Often, these features will have metadata associated with them. In this section, we will look at some of the major sources of free vector-format geospatial data.

.. tab:: 英文

    Vector-based geospatial data represents physical features as collections of points, lines, and polygons. Often, these features will have metadata associated with them. In this section, we will look at some of the major sources of free vector-format geospatial data.


OpenStreetMap
------------------

OpenStreetMap

.. tab:: 中文

    OpenStreetMap (http://openstreetmap.org) is a website where people can
    collaborate to create and edit geospatial data. It describes itself as a "free editable
    map of the whole world made by people like you."

    The following screenshot shows a portion of a street map for Onchan, Isle of Man,
    based on data from OpenStreetMap:

    .. image:: ./img/97-0.png
       :scale: 70
       :class: with-border
       :align: center

.. tab:: 英文

    OpenStreetMap (http://openstreetmap.org) is a website where people can
    collaborate to create and edit geospatial data. It describes itself as a "free editable
    map of the whole world made by people like you."

    The following screenshot shows a portion of a street map for Onchan, Isle of Man,
    based on data from OpenStreetMap:

    .. image:: ./img/97-0.png
       :scale: 70
       :class: with-border
       :align: center

数据格式
~~~~~~~~~~~~~~

Data format

.. tab:: 中文

    OpenStreetMap does not use a standard format such as shapefiles to store its data. Instead, it has developed its own XML-based format for representing geospatial data in the form of nodes (single points), ways (sequences of points that define a line), areas (closed ways that represent polygons), and relations (collections of other elements). Any element (node, way, or relation) can have a number of tags associated with it that provide additional information about the element.

    Following is an example of how the OpenStreetMap XML data looks:

    .. code-block:: XML

        <osm>
            <node id="603279517" lat="-38.1456457"
                lon="176.2441646".../>
            <node id="603279518" lat="-38.1456583"
                lon="176.2406726".../>
            <node id="603279519" lat="-38.1456540"
                lon="176.2380553".../>
            ...
            <way id="47390936"...>
                <nd ref="603279517"/>
                <nd ref="603279518"/>
                <nd ref="603279519"/>
                <tag k="highway" v="residential"/>
                <tag k="name" v="York Street"/>
            </way>
            ...
            <relation id="126207"...>
                <member type="way" ref="22930719" role=""/>
                <member type="way" ref="23963573" role=""/>
                <member type="way" ref="28562757" role=""/>
                <member type="way" ref="23963609" role=""/>
                <member type="way" ref="47475844" role=""/>
                <tag k="name" v="State Highway 30A"/>
                <tag k="ref" v="30A"/>
                <tag k="route" v="road"/>
                <tag k="type" v="route"/>
            </relation>
        </osm>

.. tab:: 英文

    OpenStreetMap does not use a standard format such as shapefiles to store its data. Instead, it has developed its own XML-based format for representing geospatial data in the form of nodes (single points), ways (sequences of points that define a line), areas (closed ways that represent polygons), and relations (collections of other elements). Any element (node, way, or relation) can have a number of tags associated with it that provide additional information about the element.

    Following is an example of how the OpenStreetMap XML data looks:

    .. code-block:: XML

        <osm>
            <node id="603279517" lat="-38.1456457"
                lon="176.2441646".../>
            <node id="603279518" lat="-38.1456583"
                lon="176.2406726".../>
            <node id="603279519" lat="-38.1456540"
                lon="176.2380553".../>
            ...
            <way id="47390936"...>
                <nd ref="603279517"/>
                <nd ref="603279518"/>
                <nd ref="603279519"/>
                <tag k="highway" v="residential"/>
                <tag k="name" v="York Street"/>
            </way>
            ...
            <relation id="126207"...>
                <member type="way" ref="22930719" role=""/>
                <member type="way" ref="23963573" role=""/>
                <member type="way" ref="28562757" role=""/>
                <member type="way" ref="23963609" role=""/>
                <member type="way" ref="47475844" role=""/>
                <tag k="name" v="State Highway 30A"/>
                <tag k="ref" v="30A"/>
                <tag k="route" v="road"/>
                <tag k="type" v="route"/>
            </relation>
        </osm>


获取和使用 OpenStreetMap 数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using OpenStreetMap data

.. tab:: 中文

    You can obtain geospatial data from OpenStreetMap in one of following three ways:

    - You can use the OpenStreetMap API to download a subset of the data you are interested in.
    - You can download the entire OpenStreetMap database, called Planet.osm, and process it locally. Note that this is a multi-gigabyte download.
    - You can make use of one of the mirror sites that provide OpenStreetMap data nicely packaged into smaller chunks and converted into other data formats. For example, you can download the data for North America on a state-by-state basis, in one of several available formats, including shapefiles.

    Let's take a closer look at each of these three options.

.. tab:: 英文

    You can obtain geospatial data from OpenStreetMap in one of following three ways:

    - You can use the OpenStreetMap API to download a subset of the data you are interested in.
    - You can download the entire OpenStreetMap database, called Planet.osm, and process it locally. Note that this is a multi-gigabyte download.
    - You can make use of one of the mirror sites that provide OpenStreetMap data nicely packaged into smaller chunks and converted into other data formats. For example, you can download the data for North America on a state-by-state basis, in one of several available formats, including shapefiles.

    Let's take a closer look at each of these three options.

OpenStreetMap API
^^^^^^^^^^^^^^^^^^^^^^

The OpenStreetMap API

.. tab:: 中文

    Using the OpenStreetMap API (http://wiki.openstreetmap.org/wiki/API), you
    can download selected data from the OpenStreetMap database in one of following
    three ways:

    - You can specify a **bounding** box defining the minimum and maximum longitude and latitude values, as shown in the following screenshot:

      .. image:: ./img/99-0.png
         :scale: 70
         :align: center
         :class: with-border
      
      The API will return all of the elements (nodes, ways, and relations), which are completely or partially inside the specified bounding box.

    - You can ask for a set of **changesets** which have been applied to the map. This returns all the changes made over a given time period, either for the entire map or just for the elements within a given bounding box.
    - You can download a specific element by ID, or all the elements which are associated with a specified element (for example, all elements belonging to a given relation).

    OpenStreetMap provides a Python module called **OsmApi**, which makes it easy
    to access the OpenStreetMap API. More information about this module can be
    found at http://wiki.openstreetmap.org/wiki/PythonOsmApi.

.. tab:: 英文

    Using the OpenStreetMap API (http://wiki.openstreetmap.org/wiki/API), you
    can download selected data from the OpenStreetMap database in one of following
    three ways:

    - You can specify a **bounding** box defining the minimum and maximum longitude and latitude values, as shown in the following screenshot:

      .. image:: ./img/99-0.png
         :scale: 70
         :align: center
         :class: with-border
      
      The API will return all of the elements (nodes, ways, and relations), which are completely or partially inside the specified bounding box.

    - You can ask for a set of **changesets** which have been applied to the map. This returns all the changes made over a given time period, either for the entire map or just for the elements within a given bounding box.
    - You can download a specific element by ID, or all the elements which are associated with a specified element (for example, all elements belonging to a given relation).

    OpenStreetMap provides a Python module called **OsmApi**, which makes it easy
    to access the OpenStreetMap API. More information about this module can be
    found at http://wiki.openstreetmap.org/wiki/PythonOsmApi.


Planet.osm
^^^^^^^^^^^^^^^^^^^^^^

Planet.osm

.. tab:: 中文

    If you choose to download the entire OpenStreetMap database for processing
    on your local computer, you will first need to download the entire Planet.osm
    database. This database is available in two formats: a compressed XML-format file
    containing all the nodes, ways, and relations in the OpenStreetMap database, or a
    special binary format called PBF that contains the same information but is smaller
    and faster to read.

    .. note::

        PBF is replacing XML as the preferred data format; libraries for reading and writing PBF files are available for various languages, including Python.

    The Planet.osm database is currently 23 GB in size if you download it in
    XML format, or 18 GB if you download it in PBF format. Both formats can
    be downloaded from http://planet.openstreetmap.org.

    The entire dump of the Planet.osm database is updated weekly, but regular "diffs"
    are produced which you can use to update your local copy of the Planet.osm
    database without having to download the entire database each time. The daily diffs
    are approximately 40 MB when they have been compressed.

.. tab:: 英文

    If you choose to download the entire OpenStreetMap database for processing
    on your local computer, you will first need to download the entire Planet.osm
    database. This database is available in two formats: a compressed XML-format file
    containing all the nodes, ways, and relations in the OpenStreetMap database, or a
    special binary format called PBF that contains the same information but is smaller
    and faster to read.

    .. note::

        PBF is replacing XML as the preferred data format; libraries for reading and writing PBF files are available for various languages, including Python.

    The Planet.osm database is currently 23 GB in size if you download it in
    XML format, or 18 GB if you download it in PBF format. Both formats can
    be downloaded from http://planet.openstreetmap.org.

    The entire dump of the Planet.osm database is updated weekly, but regular "diffs"
    are produced which you can use to update your local copy of the Planet.osm
    database without having to download the entire database each time. The daily diffs
    are approximately 40 MB when they have been compressed.



镜像站点和摘录
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Mirror sites and extracts

.. tab:: 中文

    Because of the size of the downloads, Planet.osm recommends that you use a
    mirror site rather than downloading it directly from their servers. Extracts are also
    provided, which allow you to download the data for a given area rather than the
    entire world. These mirror sites and extracts are maintained by third parties; for
    a list of the URLs, see http://wiki.openstreetmap.org/wiki/Planet.osm.

    Note that these extracts are often made available in alternative formats on the mirror
    sites, including shapefiles and direct database dumps.

.. tab:: 英文

    Because of the size of the downloads, Planet.osm recommends that you use a
    mirror site rather than downloading it directly from their servers. Extracts are also
    provided, which allow you to download the data for a given area rather than the
    entire world. These mirror sites and extracts are maintained by third parties; for
    a list of the URLs, see http://wiki.openstreetmap.org/wiki/Planet.osm.

    Note that these extracts are often made available in alternative formats on the mirror
    sites, including shapefiles and direct database dumps.


使用 OpenStreetMap 数据
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Working with OpenStreetMap data

.. tab:: 中文

    When you download Planet.osm, you will end up with an enormous file on your
    hard disk—currently it would be 250 GB if you downloaded the data in XML format.
    You have two main options for processing this file using Python:

    - You could use a library such as imposm (http://dev.omniscale.net/ imposm.parser) to read through the file and extract the information you want
    - You could import the data into a database, and then access that database from Python

    In most cases, you will want to import the data into a database before you
    attempt to work with it. To do this, use the excellent osm2pgsql tool, which is
    available at http://wiki.openstreetmap.org/wiki/Osm2pgsql. osm2pgsql
    was created to import the entire Planet.osm data into a PostgreSQL database,
    and so is highly optimized.

    Once you have imported the Planet.osm data into your local database, you can
    use the psycopg2 library, as described in Chapter 6, GIS in the Database, to access
    the OpenStreetMap data from your Python programs.

.. tab:: 英文

    When you download Planet.osm, you will end up with an enormous file on your
    hard disk—currently it would be 250 GB if you downloaded the data in XML format.
    You have two main options for processing this file using Python:

    - You could use a library such as imposm (http://dev.omniscale.net/ imposm.parser) to read through the file and extract the information you want
    - You could import the data into a database, and then access that database from Python

    In most cases, you will want to import the data into a database before you
    attempt to work with it. To do this, use the excellent osm2pgsql tool, which is
    available at http://wiki.openstreetmap.org/wiki/Osm2pgsql. osm2pgsql
    was created to import the entire Planet.osm data into a PostgreSQL database,
    and so is highly optimized.

    Once you have imported the Planet.osm data into your local database, you can
    use the psycopg2 library, as described in Chapter 6, GIS in the Database, to access
    the OpenStreetMap data from your Python programs.


TIGER
------------------

TIGER

.. tab:: 中文

    The United States Census Bureau have made available a large amount of geospatial
    data under the name **TIGER (Topologically Integrated Geographic Encoding and
    Referencing System)**. The TIGER data includes information on streets, railways,
    rivers, lakes, geographic boundaries, and legal and statistical areas such as school
    districts, and urban regions. Separate cartographic boundary and demographic files
    are also available for download.

    The following screenshot shows state and urban area outlines for California, based
    on data downloaded from the TIGER website:

    .. image:: ./img/102-0.png
       :scale: 50
       :align: center
       :class: with-border

    Because it is produced by the US government, TIGER only includes information for the United States and its protectorates (Puerto Rico, American Samoa, the Northern Mariana Islands, Guam, and the US Virgin Islands). For these areas, TIGER is an excellent source of geospatial data.

.. tab:: 英文

    The United States Census Bureau have made available a large amount of geospatial
    data under the name **TIGER (Topologically Integrated Geographic Encoding and
    Referencing System)**. The TIGER data includes information on streets, railways,
    rivers, lakes, geographic boundaries, and legal and statistical areas such as school
    districts, and urban regions. Separate cartographic boundary and demographic files
    are also available for download.

    The following screenshot shows state and urban area outlines for California, based
    on data downloaded from the TIGER website:

    .. image:: ./img/102-0.png
       :scale: 50
       :align: center
       :class: with-border

    Because it is produced by the US government, TIGER only includes information for the United States and its protectorates (Puerto Rico, American Samoa, the Northern Mariana Islands, Guam, and the US Virgin Islands). For these areas, TIGER is an excellent source of geospatial data.

数据格式
~~~~~~~~~~~~~~

Data format

.. tab:: 中文

    Up until 2006, the US Census Bureau provided the TIGER data in a custom
    text-based format called TIGER/Line. TIGER/Line files stored each type of
    record in a separate file, and required custom tools to process. Fortunately,
    OGR supports TIGER/Line files should you need to read them.

    Since 2007, all TIGER data has been produced in the form of shapefiles,
    which are (somewhat confusingly) called TIGER/Line shapefiles.

    You can download up-to-date shapefiles containing geospatial data such as
    street address ranges, landmarks, census blocks, metropolitan statistical areas,
    and school districts. For example, the "Core Based Statistical Area" shapefile
    contains the outline of each statistical area:

    .. image:: ./img/103-0.png
       :scale: 70
       :align: center
       :class: with-border

    This particular feature has the following metadata associated with it:

        ALAND 2606489666.0
        AWATER 578526971.0
        CBSAFP 18860
        CSAFP None
        FUNCSTAT S
        INTPTLAT +41.7499033
        INTPTLON -123.9809983
        LSAD M2
        MEMI 2
        MTFCC G3110
        NAME Crescent City, CA
        NAMELSAD Crescent City, CA Micropolitan Statistical Area
        PARTFLG N

    Information on these various attributes can be found in the extensive documentation available at the TIGER website.

    You can also download shapefiles which include demographic data such as population, number of houses, median age, and racial breakdown. For example, the following map tints each metropolitan area in California according to its total population:

    .. image:: ./img/104-0.png
       :scale: 70
       :align: center
       :class: with-border

.. tab:: 英文

    Up until 2006, the US Census Bureau provided the TIGER data in a custom
    text-based format called TIGER/Line. TIGER/Line files stored each type of
    record in a separate file, and required custom tools to process. Fortunately,
    OGR supports TIGER/Line files should you need to read them.

    Since 2007, all TIGER data has been produced in the form of shapefiles,
    which are (somewhat confusingly) called TIGER/Line shapefiles.

    You can download up-to-date shapefiles containing geospatial data such as
    street address ranges, landmarks, census blocks, metropolitan statistical areas,
    and school districts. For example, the "Core Based Statistical Area" shapefile
    contains the outline of each statistical area:

    .. image:: ./img/103-0.png
       :scale: 70
       :align: center
       :class: with-border

    This particular feature has the following metadata associated with it:

        ALAND 2606489666.0
        AWATER 578526971.0
        CBSAFP 18860
        CSAFP None
        FUNCSTAT S
        INTPTLAT +41.7499033
        INTPTLON -123.9809983
        LSAD M2
        MEMI 2
        MTFCC G3110
        NAME Crescent City, CA
        NAMELSAD Crescent City, CA Micropolitan Statistical Area
        PARTFLG N

    Information on these various attributes can be found in the extensive documentation available at the TIGER website.

    You can also download shapefiles which include demographic data such as population, number of houses, median age, and racial breakdown. For example, the following map tints each metropolitan area in California according to its total population:

    .. image:: ./img/104-0.png
       :scale: 70
       :align: center
       :class: with-border


获取和使用 TIGER 数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using TIGER data

.. tab:: 中文

    The TIGER datafiles can be downloaded from:

    http://www.census.gov/geo/www/tiger/index.html

    Make sure that you download the technical documentation, as it describes the
    various files you can download, and all of the attributes associated with each feature.
    For example, if you want to download a current set of urban areas for the US, the
    shapefile you are looking for is called tl_2012_us_uac10.shp and it includes
    information such as the city or town name and the size in square meters.

.. tab:: 英文

    The TIGER datafiles can be downloaded from:

    http://www.census.gov/geo/www/tiger/index.html

    Make sure that you download the technical documentation, as it describes the
    various files you can download, and all of the attributes associated with each feature.
    For example, if you want to download a current set of urban areas for the US, the
    shapefile you are looking for is called tl_2012_us_uac10.shp and it includes
    information such as the city or town name and the size in square meters.


Natural Earth
------------------

Natural Earth

.. tab:: 中文

    Natural Earth (http://www.naturalearthdata.com) is a website that provides
    public domain vector and raster map data at high, medium, and low resolutions.
    Two types of vector map data are provided:

    - **Cultural map data**: This includes polygons for country, state or province, urban area, and park outlines, as well as point and line data for populated places, roads, and railways:

    .. image:: ./img/105-0.png
       :scale: 50
       :align: center

    - **Physical map data**: This includes polygons and linestrings for land masses, coastlines, oceans, minor islands, reefs, rivers, lakes, and so on:

    .. image:: ./img/106-0.png
       :scale: 50
       :align: center

    All of this can be downloaded and used freely in your geospatial programs, making the Natural Earth site an excellent source of data for your application.

.. tab:: 英文

    Natural Earth (http://www.naturalearthdata.com) is a website that provides
    public domain vector and raster map data at high, medium, and low resolutions.
    Two types of vector map data are provided:

    - **Cultural map data**: This includes polygons for country, state or province, urban area, and park outlines, as well as point and line data for populated places, roads, and railways:

    .. image:: ./img/105-0.png
       :scale: 50
       :align: center

    - **Physical map data**: This includes polygons and linestrings for land masses, coastlines, oceans, minor islands, reefs, rivers, lakes, and so on:

    .. image:: ./img/106-0.png
       :scale: 50
       :align: center
    
    All of this can be downloaded and used freely in your geospatial programs, making the Natural Earth site an excellent source of data for your application.


数据格式
~~~~~~~~~~~~~~

Data format

.. tab:: 中文

    All the vector-format data on the Natural Earth website is provided in the form
    of shapefiles. All the data is in geographic (latitude and longitude) coordinates,
    using the standard WGS84 datum, making it very easy to use these files in your
    own application.

.. tab:: 英文

    All the vector-format data on the Natural Earth website is provided in the form
    of shapefiles. All the data is in geographic (latitude and longitude) coordinates,
    using the standard WGS84 datum, making it very easy to use these files in your
    own application.


获取和使用自然地球矢量数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using Natural Earth vector data

.. tab:: 中文

    The Natural Earth site is uniformly excellent, and downloading the files you want is
    easy; simply click on the **Get the Data** link on the main page. You can then choose the
    resolution and the type of data you are looking for, and you can choose to download
    either a single shapefile, or a number of shapefiles bundled together. Once they are
    downloaded, you can use the Python libraries discussed in the previous chapter to
    work with the contents of these shapefiles.

    The Natural Earth website is very comprehensive; it includes detailed information
    about the geospatial data you can download, and a forum where you can ask
    questions and discuss any problems you may have.

.. tab:: 英文

    The Natural Earth site is uniformly excellent, and downloading the files you want is
    easy; simply click on the **Get the Data** link on the main page. You can then choose the
    resolution and the type of data you are looking for, and you can choose to download
    either a single shapefile, or a number of shapefiles bundled together. Once they are
    downloaded, you can use the Python libraries discussed in the previous chapter to
    work with the contents of these shapefiles.

    The Natural Earth website is very comprehensive; it includes detailed information
    about the geospatial data you can download, and a forum where you can ask
    questions and discuss any problems you may have.


全球、自洽、分层、高分辨率海岸线数据库 (GSHHS)
------------------------------------------------------------------------------------

Global, self-consistent, hierarchical, high-resolution shoreline database (GSHHS)

.. tab:: 中文

    The US National Geophysical Data Center (part of the NOAA) have been working
    on a project to produce high-quality vector shoreline data for the entire world. The
    resulting database, **called the Global self-consistent, hierarchical, high-resolution
    shoreline database (GSHHS)**, includes detailed vector data for shorelines, lakes,
    and rivers at five different resolutions. The data has been broken out into four
    different "levels": ocean boundaries, lake boundaries, island-in-lake boundaries,
    and pond-on-island-in-lake boundaries.

    The following screenshot shows European shorelines, lakes, and islands, taken from
    the GSHHS database:

    .. image:: ./img/107-0.png
       :scale: 40
       :align: center
       :class: with-border

    The GSHHS has been constructed out of two public-domain geospatial databases: the World Data Bank II includes data on coastlines, lakes, and rivers, while the World Vector Shoreline only provides coastline data. Because the World Vector Shoreline database has more accurate data, but lacks information on rivers and lakes, the two databases were combined to provide the most accurate information possible. After merging the databases, the author then manually edited the data to make it consistent and to remove a number of errors. The result is a high-quality database of land and water boundaries worldwide.

    .. note::

        More information about the process used to create the GSHHS database can be found at: http://www.soest.hawaii.edu/pwessel/papers/1996/JGR_96/jgr_96.html

.. tab:: 英文

    The US National Geophysical Data Center (part of the NOAA) have been working
    on a project to produce high-quality vector shoreline data for the entire world. The
    resulting database, **called the Global self-consistent, hierarchical, high-resolution
    shoreline database (GSHHS)**, includes detailed vector data for shorelines, lakes,
    and rivers at five different resolutions. The data has been broken out into four
    different "levels": ocean boundaries, lake boundaries, island-in-lake boundaries,
    and pond-on-island-in-lake boundaries.

    The following screenshot shows European shorelines, lakes, and islands, taken from
    the GSHHS database:

    .. image:: ./img/107-0.png
       :scale: 40
       :align: center
       :class: with-border

    The GSHHS has been constructed out of two public-domain geospatial databases: the World Data Bank II includes data on coastlines, lakes, and rivers, while the World Vector Shoreline only provides coastline data. Because the World Vector Shoreline database has more accurate data, but lacks information on rivers and lakes, the two databases were combined to provide the most accurate information possible. After merging the databases, the author then manually edited the data to make it consistent and to remove a number of errors. The result is a high-quality database of land and water boundaries worldwide.

    .. note::

        More information about the process used to create the GSHHS database can be found at: http://www.soest.hawaii.edu/pwessel/papers/1996/JGR_96/jgr_96.html


数据格式
~~~~~~~~~~~~~~

Data format

.. tab:: 中文

    The GSHHS database is available in two different formats: a binary data format
    specific to the Generic Mapping Tools (http://gmt.soest.hawaii.edu), and
    as a series of shapefiles.

    .. note::

        **Generic Mapping Tools (GMT)** is a collection of tools for working with geospatial data. Because they don't have Python bindings, we won't be working with GMT in this book.

    If you download the data in shapefile format, you will end up with a total of twenty separate shapefiles, one for every combination of resolution and level:

    - The resolution represents the amount of detail in the map:

    .. csv-table::
       :header: "Resolution Code", "Resolution", "Includes"

       "c", "Crude", "Features greater than 500 sq.km."
       "l", "Low", "Features greater than 100 sq.km."
       "i", "Intermediate", "Features greater than 20 sq.km."
       "h", "High", "Features greater than 1 sq.km."
       "f", "Full", "Every feature"

    - The level indicates the type of boundaries that are included in the shapefile:

    .. csv-table::
       :header: "Level Code", "Includes"

       "1", "Ocean boundaries"
       "2", "Lake boundaries"
       "3", "Island-in-lake boundaries"
       "4", "Pond-on-island-in-lake boundaries"

    The name of the shapefile tells you the resolution and level of the included data. For example, the shapefile for ocean boundaries at full resolution would be named *GSHHS_f_L1.shp*.

    Each shapefile consists of a single layer containing the various polygon features making up the given type of boundary.

.. tab:: 英文

    The GSHHS database is available in two different formats: a binary data format
    specific to the Generic Mapping Tools (http://gmt.soest.hawaii.edu), and
    as a series of shapefiles.

    .. note::

        **Generic Mapping Tools (GMT)** is a collection of tools for working with geospatial data. Because they don't have Python bindings, we won't be working with GMT in this book.

    If you download the data in shapefile format, you will end up with a total of twenty separate shapefiles, one for every combination of resolution and level:

    - The resolution represents the amount of detail in the map:

    .. csv-table::
       :header: "Resolution Code", "Resolution", "Includes"

       "c", "Crude", "Features greater than 500 sq.km."
       "l", "Low", "Features greater than 100 sq.km."
       "i", "Intermediate", "Features greater than 20 sq.km."
       "h", "High", "Features greater than 1 sq.km."
       "f", "Full", "Every feature"

    - The level indicates the type of boundaries that are included in the shapefile:

    .. csv-table::
       :header: "Level Code", "Includes"

       "1", "Ocean boundaries"
       "2", "Lake boundaries"
       "3", "Island-in-lake boundaries"
       "4", "Pond-on-island-in-lake boundaries"

    The name of the shapefile tells you the resolution and level of the included data. For example, the shapefile for ocean boundaries at full resolution would be named *GSHHS_f_L1.shp*.

    Each shapefile consists of a single layer containing the various polygon features making up the given type of boundary.


获取 GSHHS 数据库
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining the GSHHS database

.. tab:: 中文

    The main GSHHS website can be found at:

    http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html

    The files are available in both GMT and shapefile format—unless you particularly
    want to use the Generic Mapping Tools, you will most likely want to download the
    shapefile version. Once you have downloaded the data, you can use OGR to read
    the files and extract the data from them in the usual way.

.. tab:: 英文

    The main GSHHS website can be found at:

    http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html

    The files are available in both GMT and shapefile format—unless you particularly
    want to use the Generic Mapping Tools, you will most likely want to download the
    shapefile version. Once you have downloaded the data, you can use OGR to read
    the files and extract the data from them in the usual way.


世界边界数据集
--------------------------

World Borders Dataset

.. tab:: 中文

    Many of the data sources we have examined so far are rather complex. If all you are
    looking for is some simple vector data covering the entire world, the World Borders
    Dataset may be all you need. While some of the country borders are apparently
    disputed, the simplicity of the World Borders Dataset makes it an attractive choice
    for many basic geospatial applications.

    The following map was generated using the World Borders Dataset:

    .. image:: ./img/109-0.png
       :class: with-border
       :align: center
       :scale: 35

    The World Borders Dataset will be used extensively throughout this book. Indeed, you have already seen an example program in *Chapter 3, Python Libraries for Geospatial Development*, where we used Mapnik to generate a world map using the World Borders Dataset shapefile.

.. tab:: 英文

    Many of the data sources we have examined so far are rather complex. If all you are
    looking for is some simple vector data covering the entire world, the World Borders
    Dataset may be all you need. While some of the country borders are apparently
    disputed, the simplicity of the World Borders Dataset makes it an attractive choice
    for many basic geospatial applications.

    The following map was generated using the World Borders Dataset:

    .. image:: ./img/109-0.png
       :class: with-border
       :align: center
       :scale: 35

    The World Borders Dataset will be used extensively throughout this book. Indeed, you have already seen an example program in *Chapter 3, Python Libraries for Geospatial Development*, where we used Mapnik to generate a world map using the World Borders Dataset shapefile.


数据格式
~~~~~~~~~~~~~~

Data format

.. tab:: 中文

    The World Borders Dataset is available in the form of a shapefile with a single layer
    and one feature for each country. For each country, the feature has one or more
    polygons that define the country's boundary, along with useful attributes including
    the name of the country or area, various ISO, FIPS, and UN codes identifying the
    country, a region and subregion classification, the country's population, land area,
    and latitude/longitude.

    The various codes make it easy to match the features against your own country-
    specific data, and you can also use information such as the population and area to
    highlight different countries on the map. For example, the preceding screenshot
    uses the "region" field to draw each geographic region using a different color.

.. tab:: 英文

    The World Borders Dataset is available in the form of a shapefile with a single layer
    and one feature for each country. For each country, the feature has one or more
    polygons that define the country's boundary, along with useful attributes including
    the name of the country or area, various ISO, FIPS, and UN codes identifying the
    country, a region and subregion classification, the country's population, land area,
    and latitude/longitude.

    The various codes make it easy to match the features against your own country-
    specific data, and you can also use information such as the population and area to
    highlight different countries on the map. For example, the preceding screenshot
    uses the "region" field to draw each geographic region using a different color.


获取世界边界数据集
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining World Borders Dataset

.. tab:: 中文

    The World Borders Dataset can be downloaded from:

    http://thematicmapping.org/downloads/world_borders.php

    This website also provides further details on the contents of the dataset, including links to the United Nations' website where the region and subregion codes are listed.

.. tab:: 英文

    The World Borders Dataset can be downloaded from:

    http://thematicmapping.org/downloads/world_borders.php

    This website also provides further details on the contents of the dataset, including links to the United Nations' website where the region and subregion codes are listed.
