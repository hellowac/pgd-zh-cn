其他类型地理空间数据的来源
============================================

Sources of other types of geospatial data

.. tab:: 中文

    The vector and raster geospatial data we have looked at so far is generally used to
    provide images or information about the Earth itself. However, geospatial applications
    often have to place data onto the surface of the Earth—that is, georeference something
    such as a place or event. In this section, we will look at two additional sources of
    geospatial data; in this case databases that place cities, towns, natural features, and
    points of interest onto the surface of the Earth.

    This data can be used in two important ways. First, it can be used to label features—
    for example, to place the label "London" onto a georeferenced image of southern
    England. Secondly, this data can be used to locate something by name, for example
    by allowing the user to choose a city from a drop-down list and then draw a map
    centered around that city.

.. tab:: 英文

    The vector and raster geospatial data we have looked at so far is generally used to
    provide images or information about the Earth itself. However, geospatial applications
    often have to place data onto the surface of the Earth—that is, georeference something
    such as a place or event. In this section, we will look at two additional sources of
    geospatial data; in this case databases that place cities, towns, natural features, and
    points of interest onto the surface of the Earth.

    This data can be used in two important ways. First, it can be used to label features—
    for example, to place the label "London" onto a georeferenced image of southern
    England. Secondly, this data can be used to locate something by name, for example
    by allowing the user to choose a city from a drop-down list and then draw a map
    centered around that city.

GEOnet 名称服务器
---------------------

GEOnet Names Server

.. tab:: 中文

    The GEOnet Names Server provides a large database of place names. It is an
    official repository of non-American place names, as decided by the US Board
    on geographic names.

    The following screenshot is an extract from the GEOnet Names Server database:

    .. image:: ./img/123-0.png
       :class: with-border
       :align: center

    As you can see from this example, this database includes longitude and latitude values,
    as well as codes indicating the type of place (populated place, administrative district,
    natural feature, and so on), the elevation (where relevant), and a code indicating the
    type of name (official, conventional, historical, and so on).

    The GEOnet Names Server database contains approximately 5 million features,
    with 8 million names. It includes every country other than the US and Antarctica.

.. tab:: 英文

    The GEOnet Names Server provides a large database of place names. It is an
    official repository of non-American place names, as decided by the US Board
    on geographic names.

    The following screenshot is an extract from the GEOnet Names Server database:

    .. image:: ./img/123-0.png
       :class: with-border
       :align: center

    As you can see from this example, this database includes longitude and latitude values,
    as well as codes indicating the type of place (populated place, administrative district,
    natural feature, and so on), the elevation (where relevant), and a code indicating the
    type of name (official, conventional, historical, and so on).

    The GEOnet Names Server database contains approximately 5 million features,
    with 8 million names. It includes every country other than the US and Antarctica.

数据格式
~~~~~~~~~~~~~~

Data format

.. tab:: 中文

    The GEOnet Names Server's data is provided as a simple tab-delimited text
    file, where the first row in the file contains the field names and the subsequent
    rows contains the various features, one per row. Importing this name data into
    a spreadsheet or database is trivial.

    For more information on the supplied fields and what the various codes mean,
    please refer to:

    http://earth-info.nga.mil/gns/html/gis_countryfiles.html

.. tab:: 英文

    The GEOnet Names Server's data is provided as a simple tab-delimited text
    file, where the first row in the file contains the field names and the subsequent
    rows contains the various features, one per row. Importing this name data into
    a spreadsheet or database is trivial.

    For more information on the supplied fields and what the various codes mean,
    please refer to:

    http://earth-info.nga.mil/gns/html/gis_countryfiles.html


获取和使用 GEOnet 名称服务器数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using GEOnet Names Server data

.. tab:: 中文

    The main website for the GEOnet Names Server is:

    http://earth-info.nga.mil/gns/html

    The main interface to the GEOnet Names Server is through various search tools
    that provide filtered views onto the data. To download the data directly rather
    than through searching, go to:

    http://earth-info.nga.mil/gns/html/namefiles.htm

    Each country is listed; simply click on the hyperlink for the country you want data
    on and your browser will download a .zip file containing the tab-delimited text file
    containing all the features within that country. There is also an option to download
    all the countries in one file, which is a 370 MB download.

    Once you have downloaded the file and decompressed it, you can load the file
    directly into a spreadsheet or database for further processing. By filtering on the
    **Feature Classification (FC), Feature Designation Code (DSG)**, and other fields,
    you can select the particular set of place names you want, and then use this data
    directly in your application.

.. tab:: 英文

    The main website for the GEOnet Names Server is:

    http://earth-info.nga.mil/gns/html

    The main interface to the GEOnet Names Server is through various search tools
    that provide filtered views onto the data. To download the data directly rather
    than through searching, go to:

    http://earth-info.nga.mil/gns/html/namefiles.htm

    Each country is listed; simply click on the hyperlink for the country you want data
    on and your browser will download a .zip file containing the tab-delimited text file
    containing all the features within that country. There is also an option to download
    all the countries in one file, which is a 370 MB download.

    Once you have downloaded the file and decompressed it, you can load the file
    directly into a spreadsheet or database for further processing. By filtering on the
    **Feature Classification (FC), Feature Designation Code (DSG)**, and other fields,
    you can select the particular set of place names you want, and then use this data
    directly in your application.


地理名称信息系统 (GNIS)
---------------------------------------------

Geographic Names Information System (GNIS)

.. tab:: 中文

    The **Geographic Names Information System (GNIS)** is the US equivalent of the GEOnet Names Server—it contains name information for the United States.

    The following screenshot is an extract from the GNIS database:

    .. image:: ./img/125-0.png
       :class: with-border
       :align: center

    GNIS includes natural, physical, and cultural features, though it does not include road or highway names.

    As with the GEOnames database, the GNIS database contains the official names
    used by the US Government, as decided by the US Board on Geographic Names.
    GEOnames is run by the US Geological Survey, and currently contains over 2.2
    million features.

.. tab:: 英文

    The **Geographic Names Information System (GNIS)** is the US equivalent of the GEOnet Names Server—it contains name information for the United States.

    The following screenshot is an extract from the GNIS database:

    .. image:: ./img/125-0.png
       :class: with-border
       :align: center

    GNIS includes natural, physical, and cultural features, though it does not include road or highway names.

    As with the GEOnames database, the GNIS database contains the official names
    used by the US Government, as decided by the US Board on Geographic Names.
    GEOnames is run by the US Geological Survey, and currently contains over 2.2
    million features.


数据格式
~~~~~~~~~~~~~~

Data format

.. tab:: 中文

    GNIS names are available for download as "pipe-delimited" compressed text files.
    This format uses the "pipe" character (|) to separate the various fields:
    
    .. code-block:: text

        FEATURE_ID|FEATURE_NAME|FEATURE_CLASS|...
        1397658|Ester|Populated Place|...
        1397926|Afognak|Populated Place|...

    The first line contains the field names, and subsequent lines contain the various
    features. The available information includes the name of the feature, its type, elevation,
    the county and state the feature is in, the latitude/longitude coordinate of the feature
    itself, and the latitude/longitude coordinate for the "source" of the feature (for streams,
    valleys, and so on).

.. tab:: 英文

    GNIS names are available for download as "pipe-delimited" compressed text files.
    This format uses the "pipe" character (|) to separate the various fields:

    .. code-block:: text

        FEATURE_ID|FEATURE_NAME|FEATURE_CLASS|...
        1397658|Ester|Populated Place|...
        1397926|Afognak|Populated Place|...

    The first line contains the field names, and subsequent lines contain the various
    features. The available information includes the name of the feature, its type, elevation,
    the county and state the feature is in, the latitude/longitude coordinate of the feature
    itself, and the latitude/longitude coordinate for the "source" of the feature (for streams,
    valleys, and so on).


获取和使用 GNIS 数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Obtaining and using GNIS Data

.. tab:: 中文

    The main GNIS website can be found at:

    http://geonames.usgs.gov/domestic

    Click on the **Download Domestic Names** hyperlink, and you will be given options
    to download all the GNIS data on a state-by-state basis, or all the features in a single
    large download. You can also download "topical gazetteers" that include selected
    subsets of the data—all populated places, all historical places, and so on.

    If you click on one of the file format hyperlinks, a pop-up window will appear
    describing the structure of the files in more detail.

    Once you have downloaded the data you want, you can simply import the file into
    a database or spreadsheet. To import into a spreadsheet, use the "Delimited" format
    and enter | as the custom delimiter character. You can then sort or filter the data in
    whatever way you want so that you can use it in your application.

.. tab:: 英文

    The main GNIS website can be found at:

    http://geonames.usgs.gov/domestic

    Click on the **Download Domestic Names** hyperlink, and you will be given options
    to download all the GNIS data on a state-by-state basis, or all the features in a single
    large download. You can also download "topical gazetteers" that include selected
    subsets of the data—all populated places, all historical places, and so on.

    If you click on one of the file format hyperlinks, a pop-up window will appear
    describing the structure of the files in more detail.

    Once you have downloaded the data you want, you can simply import the file into
    a database or spreadsheet. To import into a spreadsheet, use the "Delimited" format
    and enter | as the custom delimiter character. You can then sort or filter the data in
    whatever way you want so that you can use it in your application.
