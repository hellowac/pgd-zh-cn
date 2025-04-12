GIS 数据格式
=====================

GIS data formats

.. tab:: 中文

    GIS 数据格式指定了地理空间数据如何存储在磁盘上的文件（或多个文件）中。该格式描述了用于在文件中存储地理空间数据的逻辑结构。

    .. note::

        尽管我们讨论的是将信息存储在磁盘上，但数据格式也可用于在计算机系统之间传输地理空间信息。例如，Web 服务可能在请求时提供地图数据，并以特定格式传输该数据。

    GIS 数据格式通常支持：

    - 描述地理特征的地理空间数据。
    - 描述这些数据的附加元数据，包括使用的基准和投影、数据所用的坐标系统和单位、文件最后更新时间等。
    - 提供有关所描述地理特征的附加属性。例如，一个城市特征可能包含诸如“名称”、“人口”、“平均温度”等属性。
    - 显示信息，例如在显示特征时使用的颜色或线条样式。

    GIS 数据主要有两种类型： **栅格格式数据** 和 **矢量格式数据** 。栅格格式通常用于存储位图图像，例如扫描的纸质地图或航空照片。而矢量格式则使用点、线和多边形表示空间数据。矢量格式是 GIS 应用程序中最常用的格式，因为数据较小且更易于操作。

    一些常见的栅格格式包括：

    - **数字栅格图（DRG）**：用于存储纸质地图的数字扫描图
    - **数字高程模型（DEM）**：美国地质调查局（USGS）用于记录高程数据
    - **按行交错带状、按像素交错带状、带状顺序（BIL, BIP, BSQ）**：这些数据格式通常用于遥感系统

    一些常见的矢量格式包括：

    - **Shapefile**：由 ESRI 开发的开放规范，用于存储和交换 GIS 数据。Shapefile 实际上由一组文件组成，所有文件具有相同的基名，例如 *hawaii.shp*、*hawaii.shx*、*hawaii.dbf* 等。
    - **简单要素（Simple Features）**：一种 OpenGIS 标准，用于存储地理数据（点、线、多边形）及其相关属性。
    - **TIGER/Line**：由美国人口普查局以前使用的基于文本的格式，用于描述地理特征，如道路、建筑物、河流和海岸线。更近的数据以 Shapefile 格式提供，因此 TIGER/Line 格式仅用于早期人口普查局数据集。
    - **覆盖（Coverage）**：ESRI 的 ARC/INFO 系统使用的专有数据格式。

    除了这些“主要”数据格式外，还有一些所谓的“微格式”，通常用于表示单个地理空间数据。它们通常用于表示正在运行的程序中的形状，或在程序之间传输形状，但一般不用于永久存储数据。在处理地理空间数据时，您可能会遇到以下微格式：

    - **著名文本（WKT）**：一种简单的基于文本的格式，用于表示单个地理特征，如多边形或线字符串
    - **著名二进制（WKB）**：与 WKT 的替代格式，使用二进制数据而不是文本来表示单个地理特征
    - **GeoJSON**：一种基于 JSON 数据交换格式的开放格式，用于编码地理数据结构
    - **地理标记语言（GML）**：一种基于 XML 的开放标准，用于交换 GIS 数据

    每当您处理地理空间数据时，必须知道数据的格式，这样您才能从文件中提取所需的信息，并在必要时将数据从一种格式转换为另一种格式。


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