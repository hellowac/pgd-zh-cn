导入数据
============================================

Importing the data

.. tab:: 中文

    现在，我们已准备好将四组数据导入 DISTAL 数据库。我们将使用 *第 3 章“用于地理空间开发的 Python 库” 和第 5 章“使用 Python 处理地理空间数据”中讨论的技术，从这些数据集中读取数据，然后使用我们在* 第 6 章“数据库中的 GIS”中讨论的技术将它们插入数据库。

    让我们依次处理每个文件。

.. tab:: 英文

    We are now ready to import our four sets of data into the DISTAL database. We will be using the techniques discussed in *Chapter 3, Python Libraries for Geospatial Development, and Chapter 5, Working with Geospatial Data in Python*, to read the data from these data sets, and then insert them into the database using the techniques we discussed in *Chapter 6, GIS in the Database*.

    Let's work through each of the files in turn.

世界边界数据集
-----------------------------
World Borders Dataset

.. tab:: 中文

    World Borders 数据集包含一个 shapefile，其中包含每个国家的轮廓图以及多种元数据，包括国家的名称（使用 Latin-1 字符编码）。我们可以直接将其导入到我们的 `countries` 表中，使用以下 Python 代码（MySQL 版本）：

    .. code-block:: python

        import os.path
        import MySQLdb
        import osgeo.ogr

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()

        cursor.execute("USE distal")
        cursor.execute("DELETE FROM countries")
        cursor.execute("SET GLOBAL max_allowed_packet=52428800")

        srcFile = os.path.join("DISTAL-data", "TM_WORLD_BORDERS-0.3",
                                "TM_WORLD_BORDERS-0.3.shp")
        shapefile = osgeo.ogr.Open(srcFile)
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME").decode("Latin-1")
            wkt = feature.GetGeometryRef().ExportToWkt()

            cursor.execute("INSERT INTO countries (name,outline) " +
                            "VALUES (%s, GeometryFromText(%s, 4326))",
                            (name.encode("utf8"), wkt))

        connection.commit()

    这里唯一不寻常的地方是 `SET GLOBAL max_allowed_packet` 指令。此命令（适用于 MySQL 5.1 及更高版本）允许我们将较大的几何体插入数据库。如果您使用的是 MySQL 的早期版本，则需要在运行程序之前编辑 *my.cnf* 文件，并手动设置该变量。

    .. note::

        请注意，您必须将 `max_allowed_packet` 设置为 1,024 字节的倍数。在这个示例中，我们将其设置为 50 兆字节（50 x 1,024 x 1,024 = 52,428,800）。

    请注意，我们遵循了将空间参考与多边形关联的推荐最佳实践。在大多数情况下，我们将处理 WGS84 坐标系（SRID 4326）的未投影坐标，尽管显式地声明这一点可以避免在处理使用其他空间参考的数据时出现麻烦。下面是 PostGIS 的等效代码：

    .. code-block:: python

        import os.path
        import psycopg2
        import osgeo.ogr

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DELETE FROM countries")
        srcFile = os.path.join("DISTAL-data", "TM_WORLD_BORDERS-0.3",
                                "TM_WORLD_BORDERS-0.3.shp")

        shapefile = osgeo.ogr.Open(srcFile)
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME").decode("Latin-1")
            wkt = feature.GetGeometryRef().ExportToWkt()
        
        cursor.execute("INSERT INTO countries (name,outline) " +
                        "VALUES (%s, ST_GeometryFromText(%s, " +
                        "4326))", (name.encode("utf8"), wkt))
        connection.commit()

    SpatiaLite 的等效代码如下所示：

    .. code-block:: python

        import os, os.path
        from pysqlite2 import dbapi2 as sqlite
        import osgeo.ogr

        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()

        cursor.execute("DELETE FROM countries")
        srcFile = os.path.join("DISTAL-data", "TM_WORLD_BORDERS-0.3",
                                "TM_WORLD_BORDERS-0.3.shp")

        shapefile = osgeo.ogr.Open(srcFile)
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME").decode("Latin-1")
            wkt = feature.GetGeometryRef().ExportToWkt()

            cursor.execute("INSERT INTO countries (name,outline) " +
                            "VALUES (?, ST_GeometryFromText(?, " +
                            "4326))", (name, wkt))
        db.commit()

    .. note:: SpatiaLite 不支持 UTF-8 编码，因此在这种情况下，我们将国家名称直接存储为 Unicode 字符串。

.. tab:: 英文

    The World Borders Dataset consists of a shapefile containing the outline of each country along with a variety of metadata, including the country's name in Latin-1 character encoding. We can import this directly into our countries table using the following Python code for MySQL:

    .. code-block:: python

        import os.path
        import MySQLdb
        import osgeo.ogr

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()

        cursor.execute("USE distal")
        cursor.execute("DELETE FROM countries")
        cursor.execute("SET GLOBAL max_allowed_packet=52428800")

        srcFile = os.path.join("DISTAL-data", "TM_WORLD_BORDERS-0.3",
                                "TM_WORLD_BORDERS-0.3.shp")
        shapefile = osgeo.ogr.Open(srcFile)
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME").decode("Latin-1")
            wkt = feature.GetGeometryRef().ExportToWkt()

            cursor.execute("INSERT INTO countries (name,outline) " +
                            "VALUES (%s, GeometryFromText(%s, 4326))",
                            (name.encode("utf8"), wkt))

        connection.commit()

    The only unusual thing here is the SET GLOBAL max_allowed_packet instruction.
    This command (which works with MySQL Versions 5.1 and later) allows us to insert
    larger geometries into the database. If you are using an earlier version of MySQL,
    you will have to edit the *my.cnf* file and set this variable manually before running
    the program.

    .. note::
        
        Note that you must set max_allowed_packet to be a multiple of 1,024 bytes. In this example, we have set it to 50 megabytes (50 x 1,024 x 1,024 = 52,428,800).

    Note that we are following the recommended best practice of associating the spatial
    reference with the polygon. In most cases we will be dealing with unprojected
    coordinates on the WGS84 datum (SRID 4326), although stating this explicitly can save
    us some trouble when we come to dealing with data that uses other spatial references.
    Here is what the equivalent code would look like for PostGIS:

    .. code-block:: python

        import os.path
        import psycopg2
        import osgeo.ogr

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DELETE FROM countries")
        srcFile = os.path.join("DISTAL-data", "TM_WORLD_BORDERS-0.3",
                                "TM_WORLD_BORDERS-0.3.shp")

        shapefile = osgeo.ogr.Open(srcFile)
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME").decode("Latin-1")
            wkt = feature.GetGeometryRef().ExportToWkt()
        
        cursor.execute("INSERT INTO countries (name,outline) " +
                        "VALUES (%s, ST_GeometryFromText(%s, " +
                        "4326))", (name.encode("utf8"), wkt))
        connection.commit()

    The equivalent code for SpatiaLite would look like this:

    .. code-block:: python

        import os, os.path
        from pysqlite2 import dbapi2 as sqlite
        import osgeo.ogr

        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()

        cursor.execute("DELETE FROM countries")
        srcFile = os.path.join("DISTAL-data", "TM_WORLD_BORDERS-0.3",
                                "TM_WORLD_BORDERS-0.3.shp")

        shapefile = osgeo.ogr.Open(srcFile)
        layer = shapefile.GetLayer(0)

        for i in range(layer.GetFeatureCount()):
            feature = layer.GetFeature(i)
            name = feature.GetField("NAME").decode("Latin-1")
            wkt = feature.GetGeometryRef().ExportToWkt()

            cursor.execute("INSERT INTO countries (name,outline) " +
                            "VALUES (?, ST_GeometryFromText(?, " +
                            "4326))", (name, wkt))
        db.commit()

    .. note:: SpatiaLite doesn't know about UTF-8 encoding, so in this case we store the country names directly as Unicode strings.

GSHHS
-----------------------------
GSHHS

.. tab:: 中文

    GSHHS 海岸线数据库包含五个独立的 shapefile，定义了五种不同分辨率下的陆地/水域边界。对于 DISTAL 应用程序，我们希望导入四个级别的 GSHHS 数据（海岸线、湖泊、湖中的岛屿和岛中池塘）以全分辨率。我们可以直接将这些 shapefile 导入到 DISTAL 数据库中的 `shorelines` 表中。

    对于 MySQL，我们使用以下代码：

    .. code-block:: python

        import os.path
        import MySQLdb
        import osgeo.ogr

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()

        cursor.execute("USE distal")
        cursor.execute("DELETE FROM shorelines")
        cursor.execute("SET GLOBAL max_allowed_packet=52428800")

        for level in [1, 2, 3, 4]:
            srcFile = os.path.join("DISTAL-data", "GSHHS_shp", "f",
                                    "GSHHS_f_L" + str(level) + ".shp")
            shapefile = osgeo.ogr.Open(srcFile)
            layer = shapefile.GetLayer(0)

            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                wkt = feature.GetGeometryRef().ExportToWkt()

                cursor.execute("INSERT INTO shorelines " +
                                "(level,outline) VALUES " +
                                "(%s, GeometryFromText(%s, 4326))",
                                (level, wkt))

            connection.commit()

    请注意，这可能需要一分钟或两分钟才能完成，因为我们正在将超过 180,000 个多边形导入数据库。

    PostGIS 的等效代码如下：

    .. code-block:: python

        import os.path
        import psycopg2
        import osgeo.ogr

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DELETE FROM shorelines")

        for level in [1, 2, 3, 4]:
            srcFile = os.path.join("DISTAL-data", "GSHHS_shp", "f",
                                    "GSHHS_f_L" + str(level) + ".shp")
            shapefile = osgeo.ogr.Open(srcFile)
            layer = shapefile.GetLayer(0)

            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                wkt = feature.GetGeometryRef().ExportToWkt()

                cursor.execute("INSERT INTO shorelines " +
                                "(level,outline) VALUES " +
                                "(%s, ST_GeometryFromText(%s, 4326))",
                                (level, wkt))
        connection.commit()

    使用 SpatiaLite 的等效代码如下所示：

    .. code-block:: python

        import os.path
        from pysqlite2 import dbapi2 as sqlite
        import osgeo.ogr

        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()

        cursor.execute("DELETE FROM shorelines")
        
        for level in [1, 2, 3, 4]:
            srcFile = os.path.join("DISTAL-data", "GSHHS_shp", "f",
                                    "GSHHS_f_L" + str(level) + ".shp")
            shapefile = osgeo.ogr.Open(srcFile)
            layer = shapefile.GetLayer(0)

            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                wkt = feature.GetGeometryRef().ExportToWkt()

                cursor.execute("INSERT INTO shorelines " +
                                "(level,outline) VALUES " +
                                "(?, ST_GeometryFromText(?, 4326))",
                                (level, wkt))
            db.commit()

.. tab:: 英文

    The GSHHS shoreline database consists of five separate shapefiles defining the land/
    water boundary at five different resolutions. For the DISTAL application, we want
    to import the four levels of GSHHS data (coastline, lake, island-in-lake, and pond-
    in-island-in-lake) at full resolution. We can directly import these shapefiles into the
    shorelines table within our DISTAL database.

    For MySQL, we use the following code:

    .. code-block:: python

        import os.path
        import MySQLdb
        import osgeo.ogr

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()

        cursor.execute("USE distal")
        cursor.execute("DELETE FROM shorelines")
        cursor.execute("SET GLOBAL max_allowed_packet=52428800")

        for level in [1, 2, 3, 4]:
            srcFile = os.path.join("DISTAL-data", "GSHHS_shp", "f",
                                   "GSHHS_f_L" + str(level) + ".shp")
            shapefile = osgeo.ogr.Open(srcFile)
            layer = shapefile.GetLayer(0)

            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                wkt = feature.GetGeometryRef().ExportToWkt()

                cursor.execute("INSERT INTO shorelines " +
                               "(level,outline) VALUES " +
                               "(%s, GeometryFromText(%s, 4326))",
                               (level, wkt))

            connection.commit()

    Note that this might take a minute or two to complete, as we are importing more
    than 180,000 polygons into the database.

    The equivalent code for PostGIS would look like this:

    .. code-block:: python

        import os.path
        import psycopg2
        import osgeo.ogr

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DELETE FROM shorelines")

        for level in [1, 2, 3, 4]:
            srcFile = os.path.join("DISTAL-data", "GSHHS_shp", "f",
                                   "GSHHS_f_L" + str(level) + ".shp")
            shapefile = osgeo.ogr.Open(srcFile)
            layer = shapefile.GetLayer(0)

            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                wkt = feature.GetGeometryRef().ExportToWkt()

                cursor.execute("INSERT INTO shorelines " +
                               "(level,outline) VALUES " +
                               "(%s, ST_GeometryFromText(%s, 4326))",
                               (level, wkt))
        connection.commit()

    The equivalent code using SpatiaLite would look like this:

    .. code-block:: python

        import os.path
        from pysqlite2 import dbapi2 as sqlite
        import osgeo.ogr

        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()

        cursor.execute("DELETE FROM shorelines")
        
        for level in [1, 2, 3, 4]:
            srcFile = os.path.join("DISTAL-data", "GSHHS_shp", "f",
                                   "GSHHS_f_L" + str(level) + ".shp")
            shapefile = osgeo.ogr.Open(srcFile)
            layer = shapefile.GetLayer(0)

            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                wkt = feature.GetGeometryRef().ExportToWkt()

                cursor.execute("INSERT INTO shorelines " +
                               "(level,outline) VALUES " +
                               "(?, ST_GeometryFromText(?, 4326))",
                               (level, wkt))
            db.commit()

美国地名数据
-----------------------------
US place name data

.. tab:: 中文

    美国地名列表存储在您下载的名为 `NationalFile_YYYYMMDD.txt` （其中 YYYYMMDD 是时间戳）的大文本文件中。该文件是以管道符（|）分隔的文件，每列由 | 字符分隔，如下所示：

    .. code-block:: text

        FEATURE_ID|FEATURE_NAME|FEATURE_CLASS|...|DATE_EDITED
        399|Agua Sal Creek|Stream|AZ|...|02/08/1980
        400|Agua Sal Wash|Valley|AZ|...|02/08/1980

    第一行包含字段名称。虽然文件中有很多字段，但我们特别感兴趣的是以下四个字段：

    - `FEATURE_NAME` 字段包含地点名称。请注意，此字段使用 UTF-8 字符编码。
    - `FEATURE_CLASS` 字段告诉我们正在处理什么类型的特征，在此情况下是 Stream（溪流）或 Valley（山谷）。对于 DISTAL 应用程序，我们只关心一种特征类：Populated Place（有人居住的地方）。
    - `PRIM_LONG_DEC` 和 `PRIM_LAT_DEC` 字段包含地点的经度和纬度（十进制度）。根据文档，这些坐标使用的是 NAD83 基准，而不是我们导入的其他数据所使用的 WGS84 基准。NAD83 基准的未投影经纬度坐标的 SRID 值为 4269。

    处理这些数据的一种方法是创建一个临时数据库表，将整个 `NationalFile_YYYYMMDD.txt` 文件导入到其中，提取我们需要的特征类，使用 OGR 将它们从 NAD83 转换为 WGS84，然后将这些特征插入到我们的 `places` 表中。但是，这种方法有两个缺点：

    - 插入超过两百万个特征到数据库需要很长时间，而我们只需要这些特征中的一个小部分。
    - MySQL 不支持即时的几何转换，因此我们必须先从数据库读取几何数据，将其转换为 OGR 几何对象，使用 OGR 转换几何数据，然后再将其转换回 WKT 格式以便插入回数据库。

    为了避免这些麻烦，我们采用略有不同的方法：

    - 提取文件中的所有特征
    - 忽略特征类错误的条目
    - 使用 pyproj 从 NAD83 转换到 WGS84
    - 将转换后的特征直接插入 `places` 表

    除了最后一步，这种方法与数据库无关，这意味着无论您使用哪个数据库，都可以使用相同的代码：

    .. code-block:: python

        import os.path
        import pyproj

        srcProj = pyproj.Proj(proj='longlat', ellps='GRS80',
                                datum='NAD83')
        dstProj = pyproj.Proj(proj='longlat', ellps='WGS84',
                                datum='WGS84')

        f = file(os.path.join("DISTAL-data",
                                "NationalFile_YYYYMMDD.txt"), "r")

        heading = f.readline() # Ignore field names.
        
        for line in f.readlines():
            parts = line.rstrip().split("|")
            featureName = parts[1]
            featureClass = parts[2]
            lat = float(parts[9])
            long = float(parts[10])

            if featureClass == "Populated Place":
                long,lat = pyproj.transform(srcProj, dstProj,
                                            long, lat)
                ...
        f.close()

    确保使用您下载的 `NationalFile_YYYYMMDD.txt` 文件的正确名称，考虑到文件名中的时间戳。

    .. note::

        严格来说，前面的代码有些过于谨慎。我们使用 pyproj 将坐标从 NAD83 转换为 WGS84。然而，我们导入的数据都位于美国境内，这两个基准对于美国境内的点实际上是相同的。因此，pyproj 实际上不会改变坐标。不过，我们依然这样做，遵循推荐的做法，了解数据的空间参考并在必要时进行转换——即使这个转换在某些情况下是无效的。

    现在，我们可以添加特定于数据库的代码，将特征添加到我们的 `places` 表中。对于 MySQL，请在程序的开始部分添加以下代码：

    .. code-block:: python

        import MySQLdb

        connection = MySQLdb.connect(user="USERNAME", passwd="PASSWORD")
        cursor = connection.cursor()
        cursor.execute("USE distal")
        cursor.execute("DELETE FROM places")
        num_inserted = 0

    接下来，使用以下代码替换前面的 ...：

    .. code-block:: python

        cursor.execute("INSERT INTO places " +
                        "(name, position) VALUES (%s, " +
                        "GeomFromWKB(Point(%s, %s), 4326))",
                        (featureName, long, lat))

        num_inserted += 1
        if num_inserted % 1000 == 0:
            connection.commit()

    最后，添加以下代码到程序结尾：

        connection.commit()

    .. note::

        请注意，我们定期调用 `connection.commit()` 来提交更改到数据库。这有助于在插入成千上万条记录时加速程序。

    如您所见，我们的 `INSERT` 语句从转换后的纬度和经度值创建一个新的 `Point` 对象，然后使用 `GeomFromWKB()` 为几何对象分配一个 SRID 值。结果将存储在 `places` 表中的 `position` 列中。

    对于 PostGIS，等效代码如下所示：

    .. code-block:: python

        import psycopg2

        connection = psycopg2.connect("dbname=DATABASE user=USER")
        cursor = connection.cursor()
        cursor.execute("SET NAMES 'utf8'")
        cursor.execute("DELETE FROM places")

        num_inserted = 0
        ...
            cursor.execute("INSERT INTO places " +
                            "(name, position) VALUES (%s, " +
                            "ST_SetSRID(" +
                            "ST_MakePoint(%s,%s), 4326))",
                            (featureName, long, lat))

            num_inserted += 1
            if num_inserted % 1000 == 0:
                connection.commit()
        ...
        connection.commit()

    与 MySQL 示例一样，在程序顶部放置第一部分代码，第二部分代码替换 ...，并在程序结尾添加 `commit()` 语句。

    同样，在 MySQL 示例中，我们创建一个 `Point` 几何对象，然后在 SQL `INSERT` 语句中为其分配 SRID 值。

    最后，SpatiaLite 版本的代码如下：

    .. code-block:: python

        from pysqlite2 import dbapi2 as sqlite
        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()
        cursor.execute("DELETE FROM places")

        num_inserted = 0
        ...
        cursor.execute("INSERT INTO places " +
                        "(name, position) VALUES "
                        "(?, MakePoint(?, ?, 4326))",
                        (featureName.decode("utf-8"),
                        long, lat))

                num_inserted += 1
                if num_inserted % 1000 == 0:
                    db.commit()
        ...
        db.commit()

    .. note::

        因为 SpatiaLite 不知道 UTF-8 字符编码，我们将地点名称转换为 Unicode 字符串，并直接将其存储到数据库中。

.. tab:: 英文

    The list of US place names is stored in the large text file you downloaded named NationalFile_YYYYMMDD.txt (where YYYYMMDD is a timestamp). This is a pipe-delimited file, meaning that each column is separated by a | character like this:

    .. code-block:: text

        FEATURE_ID|FEATURE_NAME|FEATURE_CLASS|...|DATE_EDITED
        399|Agua Sal Creek|Stream|AZ|...|02/08/1980
        400|Agua Sal Wash|Valley|AZ|...|02/08/1980
    
    The first line contains the names of the various fields. While there are a lot of fields in the file, there are four fields that we are particularly interested in:

    - The FEATURE_NAME field contains the name of the location. Note that this field uses UTF-8 character encoding.
    - The FEATURE_CLASS field tells us what type of feature we are dealing with, in this case a Stream or a Valley. There are a lot of features we don't need for the DISTAL application, for example the names of bays, beaches, bridges, oilfields, and so on. In fact, there is only one feature class we are interested in: Populated Place.
    - The PRIM_LONG_DEC and PRIM_LAT_DEC fields contain the longitude and latitude of the location, in decimal degrees. According to the documentation, these coordinates use the NAD83 datum rather than the WGS84 datum used by the other data we are importing. Unprojected lat/long coordinates in the NAD83 datum have an SRID value of 4269.

    One way of approaching all this would be to create a temporary database table, import the entire NationalFile_YYYYMMDD.txt file into it, extract the features with our desired feature classes, translate them from NAD83 to WGS84, and finally insert the features into our places table. However, this approach has two disadvantages:

    - It would take a long time to insert more than two million features into the database, when we only want a small percentage of these features in our places table.
    - MySQL doesn't support on-the-fly transformation of geometries, so we would have to read the geometry from the database, convert it into an OGR Geometry object, transform the geometry using OGR, and then convert it back to WKT format for adding back into the database.

    To avoid all this, we'll take a slightly different approach:

    - Extract all the features from the file
    - Ignore features with the wrong feature class
    - Use pyproj to convert from NAD83 to WGS84
    - Insert the resulting features directly into the places table
    
    With the exception of this final step, this approach is completely independent of the database. This means that the same code can be used regardless of the database you are using:

    .. code-block:: python

        import os.path
        import pyproj

        srcProj = pyproj.Proj(proj='longlat', ellps='GRS80',
                              datum='NAD83')
        dstProj = pyproj.Proj(proj='longlat', ellps='WGS84',
                              datum='WGS84')

        f = file(os.path.join("DISTAL-data",
                              "NationalFile_YYYYMMDD.txt"), "r")

        heading = f.readline() # Ignore field names.
        
        for line in f.readlines():
            parts = line.rstrip().split("|")
            featureName = parts[1]
            featureClass = parts[2]
            lat = float(parts[9])
            long = float(parts[10])

            if featureClass == "Populated Place":
                long,lat = pyproj.transform(srcProj, dstProj,
                                            long, lat)
                ...
        f.close()

    Make sure you use the correct name for the NationalFile_YYYYMMDD.txt file you downloaded, allowing for the datestamp on the downloaded file.

    .. note::

        Strictly speaking, the preceding code is being somewhat pedantic. We are using pyproj to transform coordinates from NAD83 to WGS84. However, the data we are importing is all within the United States, and these two datums happen to be identical for points within the United States. Because of this, pyproj won't actually change the coordinates at all. But we will do this anyway, following the recommended practice of knowing the spatial reference for our data and transforming when necessary—even if that transformation is a no-op at times.

    We can now add the database-specific code to add the feature into our places table. For MySQL, add the following code to the start of your program:

    .. code-block:: python

        import MySQLdb

        connection = MySQLdb.connect(user="USERNAME", passwd="PASSWORD")
        cursor = connection.cursor()
        cursor.execute("USE distal")
        cursor.execute("DELETE FROM places")
        num_inserted = 0

    Next, replace the ... in the previous example with the following:

    .. code-block:: python

        cursor.execute("INSERT INTO places " +
                       "(name, position) VALUES (%s, " +
                       "GeomFromWKB(Point(%s, %s), 4326))",
                       (featureName, long, lat))

        num_inserted += 1
        if num_inserted % 1000 == 0:
            connection.commit()

    Finally, add the following line to the end::

        connection.commit()

    .. note::

        Note that we regularly call connection.commit() to commit our changes to the database. This helps to speed up our program when inserting many thousands of records.

    As you can see, our INSERT statement creates a new Point object out of the translated latitude and longitude values, and then uses GeomFromWKB() to assign an SRID value to the geometry. The result is stored into the position column within the places table.

    The same code using PostGIS would look like this:

    .. code-block:: python

        import psycopg2

        connection = psycopg2.connect("dbname=DATABASE user=USER")
        cursor = connection.cursor()
        cursor.execute("SET NAMES 'utf8'")
        cursor.execute("DELETE FROM places")

        num_inserted = 0
        ...
            cursor.execute("INSERT INTO places " +
                           "(name, position) VALUES (%s, " +
                           "ST_SetSRID(" +
                           "ST_MakePoint(%s,%s), 4326))",
                           (featureName, long, lat))

            num_inserted += 1
            if num_inserted % 1000 == 0:
                connection.commit()
        ...
        connection.commit()

    As with the MySQL example, place the first chunk of code at the top of your program, the second replaces ..., and the commit() statement goes at the end.

    As with the MySQL example, we are creating a Point geometry and then assigning an SRID value to it, all within the SQL INSERT statement.

    Finally, the SpatiaLite version would look like this:

    .. code-block:: python

        from pysqlite2 import dbapi2 as sqlite
        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()
        cursor.execute("DELETE FROM places")

        num_inserted = 0
        ...
        cursor.execute("INSERT INTO places " +
                       "(name, position) VALUES "
                       "(?, MakePoint(?, ?, 4326))",
                       (featureName.decode("utf-8"),
                       long, lat))

                num_inserted += 1
                if num_inserted % 1000 == 0:
                    db.commit()
        ...
        db.commit()

    .. note::

        Because SpatiaLite doesn't know about UTF-8 character encoding, we convert the place name to a Unicode string and store that directly into the database.


世界各地地名数据
-----------------------------
Worldwide places' name data

.. tab:: 中文

    非美国的地名列表存储在你之前下载的 `geonames_dd_dms_date_YYYYMMDD` 文件中。该文件是一个以制表符分隔的文本文件，使用 UTF-8 字符编码，其内容类似于以下格式：

    .. code-block:: text

        RC  UFI     ... FULL_NAME_ND_RG   NOTE       MODIFY_DATE
        1  -1307834 ... Pavia                        1993-12-21
        1  -1307889 ... Santa Anna        gjgscript  1993-12-21

    与美国地名数据类似，这里包含了许多我们不需要的特征，因为我们只对官方的城镇和城市名称感兴趣。因此，我们需要按以下方式过滤数据：

    - **FC（特征分类）** 字段告诉我们该特征的类型。我们只关心值为 "P"（有人居住的地方）的特征。
    - **NT（名称类型）** 字段告诉我们该特征名称的状态。我们只关心值为 "N"（批准名称）的名称。
    - **DSG（特征指定代码）** 字段比 FC 字段提供了更详细的特征类型。所有特征指定代码的完整列表可以在 http://geonames.nga.mil/ggmagaz/feadesgsearchhtml.asp 找到。我们只关心具有 "PPL"（有人居住的地方）、"PPLA"（行政首都）或 "PPLC"（首都城市）值的特征。

    此外，每个地名可能有多个版本，我们需要的是正常阅读顺序中的全名，它存储在名为 **FULL_NAME_RO** 的字段中。了解这一点后，我们可以编写一些 Python 代码，从文件中提取我们所需的特征：

    .. code-block:: python

        import os.path

        f = file(os.path.join("DISTAL-data",
                            "geonames_dd_dms_date_YYYYMMDD.txt"),
                "r")

        heading = f.readline() # 忽略字段名。

        for line in f.readlines():
            parts = line.rstrip().split("\t")
            lat = float(parts[3])
            long = float(parts[4])
            featureClass = parts[9]
            featureDesignation = parts[10]
            nameType = parts[17]
            featureName = parts[22]

            if (featureClass == "P" and nameType == "N" and
                featureDesignation in ["PPL", "PPLA", "PPLC"]):
                # 处理符合条件的特征
                ...
        f.close()

    现在我们已经获得了每个特征的名称、纬度和经度，我们可以重新使用上一节的代码将这些特征插入数据库。例如，对于 MySQL，我们需要在程序开始时添加以下代码：

    .. code-block:: python

        import MySQLdb
        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()
        cursor.execute("USE distal")
        num_inserted = 0

    然后，将 `...` 替换为以下内容：

    .. code-block:: python

        cursor.execute("INSERT INTO places " +
                        "(name, position) VALUES (%s, " +
                        "GeomFromWKB(Point(%s, %s), 4326))",
                        (featureName, long, lat))

        num_inserted += 1
        if num_inserted % 1000 == 0:
            connection.commit()

    最后，我们需要在程序末尾添加以下代码行：

        connection.commit()

    .. note::

        因为我们处理的是全球数据，所以纬度/经度值已经使用 WGS84 坐标系，因此无需在添加到数据库之前转换坐标。

    如果你使用的是 PostGIS 或 SpatiaLite，只需复制上一节中的等效代码即可。请注意，由于我们需要添加超过两百万个特征到数据库，因此该程序的执行可能需要一些时间。

.. tab:: 英文

    The list of non-US place names is stored in the geonames_dd_dms_date_YYYYMMDD file you downloaded earlier. This is a tab-delimited text file in UTF-8 character encoding, and will look something like this:

    .. code-block:: text

        RC  UFI     ... FULL_NAME_ND_RG   NOTE       MODIFY_DATE
        1  -1307834 ... Pavia                        1993-12-21
        1  -1307889 ... Santa Anna        gjgscript  1993-12-21

    As with the US places' name data, there are many more features here than we need for the DISTAL application. Since we are only interested in the official names for towns and cities, we need to filter this data in the following way:

    - The **FC (Feature Classification)** field tells us what type of feature we are dealing with. We want features with an FC value of "P" (populated place).
    - The **NT (Name Type)** field tells us the status of this feature's name. We want names with an NT value of "N" (approved name).
    - The **DSG (Feature Designation Code)** field tells us the type of feature, in more detail than the FC field. A full list of all the feature designation codes can be found at http://geonames.nga.mil/ggmagaz/feadesgsearchhtml. asp. We are interested in features with a DSG value of "PPL" (populated place), "PPLA" (administrative capital), or "PPLC" (capital city).

    There are also several different versions of each place name; we want the full name in normal reading order, which is in the field named FULL_NAME_RO. Knowing this, we can write some Python code to extract the features we want from the file:

    .. code-block:: python

        import os.path

        f = file(os.path.join("DISTAL-data",
                            "geonames_dd_dms_date_YYYYMMDD.txt"),
                "r")

        heading = f.readline() # Ignore field names.

        for line in f.readlines():
            parts = line.rstrip().split("\t")
            lat = float(parts[3])
            long = float(parts[4])
            featureClass = parts[9]
            featureDesignation = parts[10]
            nameType = parts[17]
            featureName = parts[22]

            if (featureClass == "P" and nameType == "N" and
                featureDesignation in ["PPL", "PPLA", "PPLC"]):
                ...
        f.close()

    Now that we have the name, latitude, and longitude for each of the features we
    want, we can re-use the code from the previous section to insert these features into
    the database. For example, for MySQL we would add the following to the start of
    our program:

    .. code-block:: python

        import MySQLdb
        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()
        cursor.execute("USE distal")
        num_inserted = 0

    We would then replace the ... with the following:

    .. code-block:: python

        cursor.execute("INSERT INTO places " +
                       "(name, position) VALUES (%s, " +
                       "GeomFromWKB(Point(%s, %s), 4326))",
                       (featureName, long, lat))

        num_inserted += 1
        if num_inserted % 1000 == 0:
            connection.commit()

    And finally, we would add the following code line to the end::

        connection.commit()

    .. note::

        Because we are dealing with worldwide data here, the lat/long values already use the WGS84 datum, so there is no need to translate the coordinates before adding them to the database.

    If you are using PostGIS or SpatiaLite, simply copy the equivalent code from the previous section. Note that, because there are over two million features we want to add to the database, it can take a while for this program to complete.
