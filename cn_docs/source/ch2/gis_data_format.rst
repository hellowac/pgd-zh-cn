GIS data formats
=====================

GIS data formats

.. tab:: 中文

    A GIS data format specifies how geospatial data is stored in a file (or multiple files) on disk. The format describes the logical structure used to store geospatial data within the file(s).

    .. note::

        While we talk about storing information on disk, data formats can also be used to transmit geospatial information between computer systems. For example, a web service might provide map data on request, transmitting that data in a particular format.

    A GIS data format will typically support:

    - Geospatial data describing geographical features.
    - Additional metadata describing this data, including the datum and projection used, the coordinate system and units that the data is in, the date this file was last updated, and so on.
    - Attributes providing additional information about the geographical features that are being described. For example, a city feature may have attributes such as "name", "population", "average temperature", and others.
    - Display information such as the color or line style to use when a feature is displayed.

    There are two main types of GIS data: **raster format data**, and **vector format data**. Raster formats are generally used to store bitmapped images, such as scanned paper maps or aerial photographs. Vector formats, on the other hand, represent spatial data using points, lines, and polygons. Vector formats are the most common type used by GIS applications as the data is smaller and easier to manipulate.

    Some of the more common raster formats include:

    - **Digital Raster Graphic (DRG)**: This format is used to store digital scans of paper maps
    - **Digital Elevation Model (DEM)**: Used by the US Geological Survey to record elevation data
    - **Band Interleaved by Line, Band Interleaved by Pixel, Band Sequential (BIL, BIP, BSQ)**: These data formats are typically used by remote sensing systems

    Some of the more common vector formats include:

    - **Shapefile**: An open specification, developed by ESRI, for storing and exchanging GIS data. A Shapefile actually consists of a collection of files, all with the same base name, for example, *hawaii.shp*, *hawaii.shx*, *hawaii.dbf*, and so on.
    - **Simple features**: An OpenGIS standard for storing geographical data (points, lines, polygons) along with associated attributes.
    - **TIGER/Line**: A text-based format previously used by the US Census Bureau to describe geographic features such as roads, buildings, rivers, and coastlines. More recent data comes in the Shapefile format, so the TIGER/Line format is only used for earlier Census Bureau datasets.
    - **Coverage**: A proprietary data format used by ESRI's ARC/INFO system.

    In addition to these "major" data formats, there are also so-called "micro-formats" which are often used to represent individual pieces of geospatial data. These are often used to represent shapes within a running program, or to transfer shapes from one program to another, but aren't generally used to store data permanently. As you work with geospatial data, you are likely to encounter the following micro-formats:

    - **Well-known Text (WKT)**: This is a simple text-based format for representing a single geographic feature such as a polygon or linestring
    - **Well-known Binary (WKB)**: This alternative to WKT uses binary data rather than text to represent a single geographic feature
    - **GeoJSON**: An open format for encoding geographic data structures, based on the JSON data interchange format
    - **Geography Markup Language (GML)**: An XML-based open standard for exchanging GIS data

    Whenever you work with geospatial data, you need to know which format the data is in, so that you can extract the information you need from the file(s), and, where necessary, transform the data from one format to another.

.. tab:: 英文

    A GIS data format specifies how geospatial data is stored in a file (or multiple files) on disk. The format describes the logical structure used to store geospatial data within the file(s).

    .. note::

        While we talk about storing information on disk, data formats can also be used to transmit geospatial information between computer systems. For example, a web service might provide map data on request, transmitting that data in a particular format.

    A GIS data format will typically support:

    - Geospatial data describing geographical features.
    - Additional metadata describing this data, including the datum and projection used, the coordinate system and units that the data is in, the date this file was last updated, and so on.
    - Attributes providing additional information about the geographical features that are being described. For example, a city feature may have attributes such as "name", "population", "average temperature", and others.
    - Display information such as the color or line style to use when a feature is displayed.

    There are two main types of GIS data: **raster format data**, and **vector format data**. Raster formats are generally used to store bitmapped images, such as scanned paper maps or aerial photographs. Vector formats, on the other hand, represent spatial data using points, lines, and polygons. Vector formats are the most common type used by GIS applications as the data is smaller and easier to manipulate.

    Some of the more common raster formats include:

    - **Digital Raster Graphic (DRG)**: This format is used to store digital scans of paper maps
    - **Digital Elevation Model (DEM)**: Used by the US Geological Survey to record elevation data
    - **Band Interleaved by Line, Band Interleaved by Pixel, Band Sequential (BIL, BIP, BSQ)**: These data formats are typically used by remote sensing systems

    Some of the more common vector formats include:

    - **Shapefile**: An open specification, developed by ESRI, for storing and exchanging GIS data. A Shapefile actually consists of a collection of files, all with the same base name, for example, *hawaii.shp*, *hawaii.shx*, *hawaii.dbf*, and so on.
    - **Simple features**: An OpenGIS standard for storing geographical data (points, lines, polygons) along with associated attributes.
    - **TIGER/Line**: A text-based format previously used by the US Census Bureau to describe geographic features such as roads, buildings, rivers, and coastlines. More recent data comes in the Shapefile format, so the TIGER/Line format is only used for earlier Census Bureau datasets.
    - **Coverage**: A proprietary data format used by ESRI's ARC/INFO system.

    In addition to these "major" data formats, there are also so-called "micro-formats" which are often used to represent individual pieces of geospatial data. These are often used to represent shapes within a running program, or to transfer shapes from one program to another, but aren't generally used to store data permanently. As you work with geospatial data, you are likely to encounter the following micro-formats:

    - **Well-known Text (WKT)**: This is a simple text-based format for representing a single geographic feature such as a polygon or linestring
    - **Well-known Binary (WKB)**: This alternative to WKT uses binary data rather than text to represent a single geographic feature
    - **GeoJSON**: An open format for encoding geographic data structures, based on the JSON data interchange format
    - **Geography Markup Language (GML)**: An XML-based open standard for exchanging GIS data

    Whenever you work with geospatial data, you need to know which format the data is in, so that you can extract the information you need from the file(s), and, where necessary, transform the data from one format to another.