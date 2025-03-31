下载数据
============================================

Downloading the data

.. tab:: 中文

    如前一节所述，DISTAL 应用程序将使用四个独立的、免费提供的地理空间数据集：

    - **世界边界数据集 (World Borders Dataset)**
    - **高分辨率 GSHHS 海岸线数据库 (GSHHS shoreline database)**
    - **美国地名数据库 (GNIS Database of US place names)**
    - **GEONet 名称服务器的非美国地名列表 (GEONet Names Server's list of non-US place names)**

    .. note:: 有关这些数据源的更多信息，请参阅第 4 章，《地理空间数据来源》

    为了跟踪我们下载的数据，请创建一个类似于 DISTAL-data 的目录。接下来，便是下载我们所需的信息。

.. tab:: 英文

    As mentioned in the previous section, the DISTAL application will make use of four separate sets of freely-available geospatial data:

    - The World Borders Dataset
    - The high-resolution GSHHS shoreline database
    - The GNIS Database of US place names
    - The GEONet Names Server's list of non-US place names

    .. note:: For more information on these sources of data, please refer to Chapter 4, Sources of Geospatial Data

    To keep track of the data as we download it, create a directory named something similar to DISTAL-data. Then it's time to download the information we need.

世界边界数据集
-----------------------
World Borders Dataset

.. tab:: 中文

    如果您还没有下载，请从以下网址下载 **世界边界数据集 (World Borders Dataset)**：

    http://thematicmapping.org/downloads/world_borders.php

    当您解压 **TM_WORLD_BORDERS-0.3.zip** 压缩包时，您将得到一个包含世界边界数据集的文件夹，文件格式为 shapefile。将此文件夹移动到您的 **DISTAL-data** 目录中。

.. tab:: 英文

    If you haven't already done so, download the World Borders Dataset from:

    http://thematicmapping.org/downloads/world_borders.php

    When you decompress the TM_WORLD_BORDERS-0.3.zip archive, you will end up with a folder containing the World Borders Dataset in shapefile format. Move this folder into your DISTAL-data directory.


GSHHS
-----------------------
GSHHS

.. tab:: 中文

    接下来，我们需要下载 **GSHHS 海岸线数据库** 的 shapefile 格式数据。如果您还没有下载，可以通过以下网址找到数据库：

    http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html

    解压 .zip 格式的压缩包后，您将得到一个名为 **GSHHS_shp** 的文件夹，里面包含二十个独立的 shapefile 文件。将该文件夹移动到您的 **DISTAL-data** 目录中。

.. tab:: 英文

    We next need to download the GSHHS shoreline database in shapefile format. If you haven't already downloaded it, the database can be found at:

    http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html

    Decompress the .zip format archive and move the resulting GSHHS_shp folder (which itself contains twenty separate shapefiles) into your DISTAL-data directory.


GNIS
-----------------------
GNIS

.. tab:: 中文

    对于美国地名数据库，请访问以下网址：

    http://geonames.usgs.gov/domestic

    点击 **Download Domestic Names** 超链接，然后选择 **Download all national features in one .zip file** 选项。这将下载一个名为 **NationalFile_YYYYMMDD.zip** 的文件，其中 YYYYMMDD 是文件的日期戳，表示文件的最后更新时间。再次解压缩得到的 .zip 格式压缩包，并将 **NationalFile_YYYYMMDD.txt** 文件移动到您的 **DISTAL-data** 目录中。

.. tab:: 英文

    For the database of US place names, go to:

    http://geonames.usgs.gov/domestic

    Click on the **Download Domestic Names** hyperlink, and choose the **Download all national features in one .zip file** option. This will download a file named NationalFile_YYYYMMDD.zip, where YYYYMMDD is the datestamp identifying when the file was last updated. Once again, decompress the resulting .zip format archive and move the NationalFile_YYYYMMDD.txt file into your DISTAL-data directory.


GEOnet 名称服务器
-----------------------
GEOnet Names Server

.. tab:: 中文

    最后，下载非美国地名数据库，请访问以下网址：

    http://earth-info.nga.mil/gns/html/namefiles.htm

    点击选项下载一个压缩的 ZIP 文件，其中包含整个国家文件数据集。这个文件较大（压缩后约 370 MB），包含了我们需要的全球所有地名信息。下载后生成的文件名为 **geonames_dd_dms_date_YYYYMMDD.zip** ，其中 **YYYYMMDD** 是文件的日期戳，表示文件的最后更新时间。

    不要被这些混乱的命名搞混：我们从 Geonames 网站下载的文件名为 **NationalFile**，而从 GEOnet Names Server 下载的文件名为 **geonames**。从现在开始，我们将直接引用文件名，而不再提及下载的具体网站。

    解压缩该 .zip 格式的压缩包，并将生成的 **geonames_dd_dms_date_YYYYMMDD.txt** 文件移动到 **DISTAL-data** 目录中。

.. tab:: 英文

    Finally, to download the database of non-US place names, go to:

    http://earth-info.nga.mil/gns/html/namefiles.htm

    Click on the option to download a single compressed ZIP file that contains the entire
    country files dataset. This is a large download (370 MB compressed) that contains
    all the place name information we need worldwide. The resulting file will be named
    geonames_dd_dms_date_YYYYMMDD.zip, where once again YYYMMDD is the datestamp
    identifying when the file was last updated.

    Don't get fooled by the confusing names here: we go to the Geonames
    website to download a file named NationalFile, and to the GEOnet
    Names Server to download a file named geonames. From now on,
    we'll refer to the name of the file rather than the website it came from.

    Decompress the .zip format archive, and move the resulting geonames_dd_dms_date_YYYYMMDD.txt file into the DISTAL-data directory.
