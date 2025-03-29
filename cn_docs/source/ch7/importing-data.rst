导入数据
============================================

Importing the data

.. tab:: 中文

    We are now ready to import our four sets of data into the DISTAL database. We will be using the techniques discussed in *Chapter 3, Python Libraries for Geospatial Development, and Chapter 5, Working with Geospatial Data in Python*, to read the data from these data sets, and then insert them into the database using the techniques we discussed in *Chapter 6, GIS in the Database*.

    Let's work through each of the files in turn.

.. tab:: 英文

    We are now ready to import our four sets of data into the DISTAL database. We will be using the techniques discussed in *Chapter 3, Python Libraries for Geospatial Development, and Chapter 5, Working with Geospatial Data in Python*, to read the data from these data sets, and then insert them into the database using the techniques we discussed in *Chapter 6, GIS in the Database*.

    Let's work through each of the files in turn.

世界边界数据集
-----------------------------
World Borders Dataset

.. tab:: 中文

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

.. tab:: 英文


GSHHS
-----------------------------
GSHHS

.. tab:: 中文

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
