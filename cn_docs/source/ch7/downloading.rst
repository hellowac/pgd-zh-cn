下载数据
============================================

Downloading the data

.. tab:: 中文

    As mentioned in the previous section, the DISTAL application will make use of four separate sets of freely-available geospatial data:

    - The World Borders Dataset
    - The high-resolution GSHHS shoreline database
    - The GNIS Database of US place names
    - The GEONet Names Server's list of non-US place names

    .. note:: For more information on these sources of data, please refer to Chapter 4, Sources of Geospatial Data

    To keep track of the data as we download it, create a directory named something similar to DISTAL-data. Then it's time to download the information we need.

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

    If you haven't already done so, download the World Borders Dataset from:

    http://thematicmapping.org/downloads/world_borders.php

    When you decompress the TM_WORLD_BORDERS-0.3.zip archive, you will end up with a folder containing the World Borders Dataset in shapefile format. Move this folder into your DISTAL-data directory.

.. tab:: 英文

    If you haven't already done so, download the World Borders Dataset from:

    http://thematicmapping.org/downloads/world_borders.php

    When you decompress the TM_WORLD_BORDERS-0.3.zip archive, you will end up with a folder containing the World Borders Dataset in shapefile format. Move this folder into your DISTAL-data directory.


GSHHS
-----------------------
GSHHS

.. tab:: 中文

    We next need to download the GSHHS shoreline database in shapefile format. If you haven't already downloaded it, the database can be found at:

    http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html

    Decompress the .zip format archive and move the resulting GSHHS_shp folder (which itself contains twenty separate shapefiles) into your DISTAL-data directory.

.. tab:: 英文

    We next need to download the GSHHS shoreline database in shapefile format. If you haven't already downloaded it, the database can be found at:

    http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html

    Decompress the .zip format archive and move the resulting GSHHS_shp folder (which itself contains twenty separate shapefiles) into your DISTAL-data directory.


GNIS
-----------------------
GNIS

.. tab:: 中文

    For the database of US place names, go to:

    http://geonames.usgs.gov/domestic

    Click on the **Download Domestic Names** hyperlink, and choose the **Download all national features in one .zip file** option. This will download a file named NationalFile_YYYYMMDD.zip, where YYYYMMDD is the datestamp identifying when the file was last updated. Once again, decompress the resulting .zip format archive and move the NationalFile_YYYYMMDD.txt file into your DISTAL-data directory.

.. tab:: 英文

    For the database of US place names, go to:

    http://geonames.usgs.gov/domestic

    Click on the **Download Domestic Names** hyperlink, and choose the **Download all national features in one .zip file** option. This will download a file named NationalFile_YYYYMMDD.zip, where YYYYMMDD is the datestamp identifying when the file was last updated. Once again, decompress the resulting .zip format archive and move the NationalFile_YYYYMMDD.txt file into your DISTAL-data directory.


GEOnet 名称服务器
-----------------------
GEOnet Names Server

.. tab:: 中文

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
