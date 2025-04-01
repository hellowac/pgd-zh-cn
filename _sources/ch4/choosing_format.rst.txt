选择地理空间数据源
============================================

Choosing your geospatial data source

.. tab:: 中文

    如果您需要获取地图数据、图像、海拔高度或地点名称用于地理空间应用程序，以上我们讨论的来源应该能为您提供所需的所有数据。当然，这并不是一个详尽无遗的列表，还有其他数据源可以在线找到，您可以使用搜索引擎或像 http://freegis.org 这样的网站进行查找。

    以下表格列出了在应用程序开发中可能需要的各种地理空间数据要求，以及每种情况下最合适的数据源：

    .. csv-table::
        :header: "需求", "合适的数据源"

        "简单的基础地图", "世界边界数据集"
        "阴影地形（伪3D）地图", "通过 gdaldem 处理的 GLOBE 或 NED 数据；自然地球栅格图像"
        "街道地图", "OpenStreetMap"
        "城市轮廓", "TIGER（美国）；自然地球城市区域"
        "详细的国家轮廓", "GSHHS 级别 1"
        "地球的真实影像", "Landsat"
        "城市和地点名称", "GNIS（美国）或 GEOnet 名称服务器（其他地区）"

.. tab:: 英文

    If you need to obtain map data, images, elevations, or place names for use in your
    geospatial applications, the sources we have covered should give you everything
    you need. Of course, this is not an exhaustive list—other sources of data are available,
    and can be found online using a search engine or sites such as http://freegis.org.

    The following table lists the various requirements you may have for geospatial data
    in your application development, and which data source(s) may be most appropriate
    in each case:

    .. csv-table::
        :header: "Requirement", "Suitable data sources"

        "Simple base map", "World Borders Dataset"
        "Shaded relief (pseudo-3D) maps", "GLOBE or NED data processed using gdaldem; Natural Earth raster images"
        "Street map", "OpenStreetMap"
        "City outlines", "TIGER (US); Natural Earth urban areas"
        "Detailed country outlines", "GSHHS Level 1"
        "Photorealistic images of the Earth", "Landsat"
        "City and place names", "GNIS (US) or Geonet Names Server (elsewhere)"