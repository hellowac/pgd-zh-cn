使用 Python 处理地理空间数据库
============================================

Working with geospatial databases using python

.. tab:: 中文

    In this section, we will build on what we've learned so far by writing a short program to (i) create a geospatial database, (ii) import data from a shapefile, (iii) perform a spatial query on that data, and (iv) save the results in WKT format. We will write the same program using each of the three databases we have explored in this chapter, so that you can see the differences and issues involved with using each particular database.

.. tab:: 英文

    In this section, we will build on what we've learned so far by writing a short program to (i) create a geospatial database, (ii) import data from a shapefile, (iii) perform a spatial query on that data, and (iv) save the results in WKT format. We will write the same program using each of the three databases we have explored in this chapter, so that you can see the differences and issues involved with using each particular database.

先决条件
--------------------------------
Prerequisites

.. tab:: 中文

    Before you can run these examples, you will need to do the following:

    1. If you haven't already done so, follow the instructions given earlier in this chapter to install MySQL, PostGIS, and SpatiaLite onto your computer.
    2. We will be working with the GSHHS shoreline dataset from Chapter 4, Sources of Geospatial Data. If you haven't already downloaded this dataset, you can download the shapefiles from:
    
       http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html
    
    3. Take a copy of the l (low-resolution) shapefiles from the GSHHS shoreline dataset and place them in a convenient directory (we will call this directory GSHHS_l in the code samples shown here).
    
    .. note:: 
        
        We will use the low-resolution shapefiles to keep the amount of data manageable, and to avoid problems with large polygons triggering a "Got a packet bigger than max_allowed_packet bytes" error in MySQL. Large polygons are certainly supported by MySQL (by increasing the max_allowed_packet setting), but doing so is beyond the scope of this chapter. We'll learn more about this setting in the next chapter.

.. tab:: 英文

    Before you can run these examples, you will need to do the following:

    1. If you haven't already done so, follow the instructions given earlier in this chapter to install MySQL, PostGIS, and SpatiaLite onto your computer.
    2. We will be working with the GSHHS shoreline dataset from Chapter 4, Sources of Geospatial Data. If you haven't already downloaded this dataset, you can download the shapefiles from:
    
       http://www.ngdc.noaa.gov/mgg/shorelines/gshhs.html
    
    3. Take a copy of the l (low-resolution) shapefiles from the GSHHS shoreline dataset and place them in a convenient directory (we will call this directory GSHHS_l in the code samples shown here).
    
    .. note:: 
        
        We will use the low-resolution shapefiles to keep the amount of data manageable, and to avoid problems with large polygons triggering a "Got a packet bigger than max_allowed_packet bytes" error in MySQL. Large polygons are certainly supported by MySQL (by increasing the max_allowed_packet setting), but doing so is beyond the scope of this chapter. We'll learn more about this setting in the next chapter.


使用 MySQL
--------------------------------
Working with MySQL

.. tab:: 中文

    We have already seen how to connect to MySQL and create a database table:

    .. code-block:: python

        import MySQLdb
        connection = MySQLdb.connect(user="..." passwd="...")

        cursor = connection.cursor()
        cursor.execute("DROP DATABASE IF EXISTS spatialTest")
        cursor.execute("CREATE DATABASE spatialTest")
        cursor.execute("USE spatialTest")
        cursor.execute("""CREATE TABLE gshhs (
                            id      INTEGER AUTO_INCREMENT,
                            level   INTEGER,
                            geom    POLYGON NOT NULL,

                            PRIMARY KEY (id)
                            INDEX (level),
                            SPATIAL INDEX (geom)) ENGINE=MyISAM
                        """)
        connection.commit()

    We next need to read the features from the GSHHS shapefiles and insert them into the database:

    .. code-block:: python

        import os.path
        from osgeo import ogr

        for level in [1, 2, 3, 4]:
            fName = os.path.join("GSHHS_l", "GSHHS_l_L"+str(level)+".shp")
            
            shapefile = ogr.Open(fName)
            layer = shapefile.GetLayer(0)
            
            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                geometry = feature.GetGeometryRef()
                wkt = geometry.ExportToWkt()

                cursor.execute("INSERT INTO gshhs (level, geom) " +
                               "VALUES (%s, GeomFromText(%s, 4326))",
                               (level, wkt))
            connection.commit()

    .. note::

        Note that we are assigning an SRID value (4326) to the features
        as we import them into the database. Even though we don't have
        a spatial_ref_sys table in MySQL, we are following the best
        practices by storing SRID values in the database.

    We now want to query the database to find the shoreline information we want.
    In this case, we'll take the coordinate for London and search for a level 1 (ocean
    boundary) polygon that contains this point. This will give us the shoreline for the
    United Kingdom:

    .. code-block:: python

        import shapely.wkt

        LONDON = 'POINT(-0.1263 51.4980)'

        cursor.execute("SELECT id,AsText(geom) FROM gshhs " +
                       "WHERE (level=%s) AND " +
                       "(MBRContains(geom, GeomFromText(%s, 4326)))",
                       (1, LONDON))
        
        shoreline = None
        for id,wkt in cursor:
            polygon = shapely.wkt.loads(wkt)
            point   = shapely.wkt.loads(LONDON)
            if polygon.contains(point):
                shoreline = wkt

    .. note::
                
        Remember that MySQL only supports bounding-rectangle
        queries, so we have to use Shapely to identify if the point
        is actually within the polygon, rather than just within its
        minimum bounding rectangle.

    To check that this query can be run efficiently, we will follow the recommended best practice of asking the MySQL Query Optimizer what it will do with the query:

    .. code-block:: console

        % /usr/local/mysql/bin/mysql
        mysql> use myDatabase;
        mysql> EXPLAIN SELECT id,AsText(geom) FROM gshhs
                WHERE (level=1) AND (MBRContains(geom,
                GeomFromText('POINT(-0.1263 51.4980)',
                4326)))\G

        *********************** 1. row ***********************
                   id: 1
          select_type: SIMPLE
                table: gshhs
                 type: range
        possible_keys: level,geom
                  key: geom
              key_len: 34
                  ref: NULL
                 rows: 1
                Extra: Using where
        1 row in set (0.00 sec)

    As you can see, we simply retyped the query, adding the word EXPLAIN to the front
    and filling in the parameters to make a valid SQL statement. The result tells us that
    the SELECT query is indeed using the indexed geom column, allowing it to quickly
    find the desired feature.

    Now that we have a working program that can quickly retrieve the desired
    geometry, let's save the UK shoreline polygon to a text file:

    .. code-block:: python

        f = file("uk-shoreline.wkt", "w")
        f.write(shoreline)
        f.close()

    Running this program saves a low-resolution outline of the United Kingdom's shoreline into the uk-shoreline.wkt file:

    .. image:: ./img/215-0.png
       :scale: 50
       :class: with-border
       :align: center

.. tab:: 英文

    We have already seen how to connect to MySQL and create a database table:

    .. code-block:: python

        import MySQLdb
        connection = MySQLdb.connect(user="..." passwd="...")

        cursor = connection.cursor()
        cursor.execute("DROP DATABASE IF EXISTS spatialTest")
        cursor.execute("CREATE DATABASE spatialTest")
        cursor.execute("USE spatialTest")
        cursor.execute("""CREATE TABLE gshhs (
                            id      INTEGER AUTO_INCREMENT,
                            level   INTEGER,
                            geom    POLYGON NOT NULL,

                            PRIMARY KEY (id)
                            INDEX (level),
                            SPATIAL INDEX (geom)) ENGINE=MyISAM
                        """)
        connection.commit()

    We next need to read the features from the GSHHS shapefiles and insert them into the database:

    .. code-block:: python

        import os.path
        from osgeo import ogr

        for level in [1, 2, 3, 4]:
            fName = os.path.join("GSHHS_l", "GSHHS_l_L"+str(level)+".shp")
            
            shapefile = ogr.Open(fName)
            layer = shapefile.GetLayer(0)
            
            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                geometry = feature.GetGeometryRef()
                wkt = geometry.ExportToWkt()

                cursor.execute("INSERT INTO gshhs (level, geom) " +
                               "VALUES (%s, GeomFromText(%s, 4326))",
                               (level, wkt))
            connection.commit()

    .. note::

        Note that we are assigning an SRID value (4326) to the features
        as we import them into the database. Even though we don't have
        a spatial_ref_sys table in MySQL, we are following the best
        practices by storing SRID values in the database.

    We now want to query the database to find the shoreline information we want.
    In this case, we'll take the coordinate for London and search for a level 1 (ocean
    boundary) polygon that contains this point. This will give us the shoreline for the
    United Kingdom:

    .. code-block:: python

        import shapely.wkt

        LONDON = 'POINT(-0.1263 51.4980)'

        cursor.execute("SELECT id,AsText(geom) FROM gshhs " +
                       "WHERE (level=%s) AND " +
                       "(MBRContains(geom, GeomFromText(%s, 4326)))",
                       (1, LONDON))
        
        shoreline = None
        for id,wkt in cursor:
            polygon = shapely.wkt.loads(wkt)
            point   = shapely.wkt.loads(LONDON)
            if polygon.contains(point):
                shoreline = wkt

    .. note::
                
        Remember that MySQL only supports bounding-rectangle
        queries, so we have to use Shapely to identify if the point
        is actually within the polygon, rather than just within its
        minimum bounding rectangle.

    To check that this query can be run efficiently, we will follow the recommended best practice of asking the MySQL Query Optimizer what it will do with the query:

    .. code-block:: console

        % /usr/local/mysql/bin/mysql
        mysql> use myDatabase;
        mysql> EXPLAIN SELECT id,AsText(geom) FROM gshhs
                WHERE (level=1) AND (MBRContains(geom,
                GeomFromText('POINT(-0.1263 51.4980)',
                4326)))\G

        *********************** 1. row ***********************
                   id: 1
          select_type: SIMPLE
                table: gshhs
                 type: range
        possible_keys: level,geom
                  key: geom
              key_len: 34
                  ref: NULL
                 rows: 1
                Extra: Using where
        1 row in set (0.00 sec)

    As you can see, we simply retyped the query, adding the word EXPLAIN to the front
    and filling in the parameters to make a valid SQL statement. The result tells us that
    the SELECT query is indeed using the indexed geom column, allowing it to quickly
    find the desired feature.

    Now that we have a working program that can quickly retrieve the desired
    geometry, let's save the UK shoreline polygon to a text file:

    .. code-block:: python

        f = file("uk-shoreline.wkt", "w")
        f.write(shoreline)
        f.close()

    Running this program saves a low-resolution outline of the United Kingdom's shoreline into the uk-shoreline.wkt file:

    .. image:: ./img/215-0.png
       :scale: 50
       :class: with-border
       :align: center


使用 PostGIS
--------------------------------
Working with PostGIS

.. tab:: 中文

    Let's rewrite this program to use PostGIS. The first part, where we open the database and define our gshhs table, is almost identical:

    .. code-block:: python

        import psycopg2

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DROP TABLE IF EXISTS gshhs")
        cursor.execute("""CREATE TABLE gshhs (
                        id
                        SERIAL,
                        level
                        INTEGER,
                        PRIMARY KEY (id))
                        """)

        cursor.execute("CREATE INDEX levelIndex ON gshhs(level)")
        cursor.execute("SELECT AddGeometryColumn('gshhs', " +
                       "'geom', 4326, 'POLYGON', 2)")
        cursor.execute("CREATE INDEX geomIndex ON gshhs " +
                       "USING GIST (geom)")
        connection.commit()

    The only difference is that we have to use the psycopg2 database adapter, and the
    fact that we have to create the geometry column (and spatial index) separately from
    the *CREATE TABLE* statement itself.

    The second part of this program where we import the data from the shapefile into
    the database is once again almost identical to the MySQL version:

    .. code-block:: python

        import os.path
        from osgeo import ogr

        for level in [1, 2, 3, 4]:
            fName = os.path.join("GSHHS_l",
                                 "GSHHS_l_L"+str(level)+".shp")
            shapefile = ogr.Open(fName)
            layer = shapefile.GetLayer(0)
            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                geometry = feature.GetGeometryRef()
                wkt = geometry.ExportToWkt()
                cursor.execute("INSERT INTO gshhs (level, geom) " +
                               "VALUES (%s, ST_GeomFromText(%s, " +
                               "4326))", (level, wkt))
            connection.commit()

    Now that we have brought the shapefile's contents into the database, we need to do
    something in PostGIS that isn't necessary with MySQL or SpatiaLite: we need to run
    a VACUUM ANALYZE command so that PostGIS can gather statistics to help it optimize
    our database queries:

    .. code-block:: python

        old_level = connection.isolation_level
        connection.set_isolation_level(0)
        cursor.execute("VACUUM ANALYZE")
        connection.set_isolation_level(old_level)

    We next want to search for the UK shoreline based upon the coordinate for
    London. This code is simpler than the MySQL version, thanks to the fact that
    PostGIS automatically does the bounding box check followed by the full
    polygon check, so we don't have to do this by hand:

    .. code-block:: python

        LONDON = 'POINT(-0.1263 51.4980)'

        cursor.execute("SELECT id,ST_AsText(geom) FROM gshhs " +
                        "WHERE (level=%s) AND " +
                        "(ST_Contains(geom, ST_GeomFromText(%s, 4326)))",
                        (1, LONDON))

        shoreline = None
        for id,wkt in cursor:
            shoreline = wkt

    Following the recommended best practices, we will ask PostGIS to tell us how it thinks this query will be performed:

    .. code-block:: python

        % usr/local/pgsql/bin/psql -U userName -d dbName
        psql> EXPLAIN SELECT id,ST_AsText(geom) FROM gshhs
        WHERE (level=2) AND (ST_Contains(geom,
        ST_GeomFromText('POINT(-0.1263 51.4980)', 4326)));

                                QUERY PLAN
        ---------------------------------------------------------
        Index Scan using geomindex on gshhs (cost=0.00..8.53 rows=1
        width=673)
        Index Cond: (geom && '0101000020E6100000ED0DBE30992AC0BF39B4C876BEB
            F4940'::geometry)
        Filter: ((level = 2) AND _st_contains(geom, '0101000020E6100000ED0D
            BE30992AC0BF39B4C876BEBF4940'::geometry))
        (3 rows)

    This tells us that PostGIS will answer this query by scanning through the geomindex
    spatial index, first filtering by bounding box (using the && operator), and then calling
    ST_Contains() to see if the polygon actually contains the desired point.

    This is exactly what we were hoping to see; the database is processing this query as
    quickly as possible while still giving us completely accurate results.

    Now that we have the desired shoreline polygon, let's finish our program by saving
    the polygon's WKT representation to disk:

    .. code-block:: python

        f = file("uk-shoreline.wkt", "w")
        f.write(shoreline)
        f.close()

    As with the MySQL version, running this program will create the uk-shoreline.wkt file containing the same low-resolution outline of the United Kingdom's shoreline.

.. tab:: 英文

    Let's rewrite this program to use PostGIS. The first part, where we open the database and define our gshhs table, is almost identical:

    .. code-block:: python

        import psycopg2

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DROP TABLE IF EXISTS gshhs")
        cursor.execute("""CREATE TABLE gshhs (
                        id
                        SERIAL,
                        level
                        INTEGER,
                        PRIMARY KEY (id))
                        """)

        cursor.execute("CREATE INDEX levelIndex ON gshhs(level)")
        cursor.execute("SELECT AddGeometryColumn('gshhs', " +
                       "'geom', 4326, 'POLYGON', 2)")
        cursor.execute("CREATE INDEX geomIndex ON gshhs " +
                       "USING GIST (geom)")
        connection.commit()

    The only difference is that we have to use the psycopg2 database adapter, and the
    fact that we have to create the geometry column (and spatial index) separately from
    the *CREATE TABLE* statement itself.

    The second part of this program where we import the data from the shapefile into
    the database is once again almost identical to the MySQL version:

    .. code-block:: python

        import os.path
        from osgeo import ogr

        for level in [1, 2, 3, 4]:
            fName = os.path.join("GSHHS_l",
                                 "GSHHS_l_L"+str(level)+".shp")
            shapefile = ogr.Open(fName)
            layer = shapefile.GetLayer(0)
            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                geometry = feature.GetGeometryRef()
                wkt = geometry.ExportToWkt()
                cursor.execute("INSERT INTO gshhs (level, geom) " +
                               "VALUES (%s, ST_GeomFromText(%s, " +
                               "4326))", (level, wkt))
            connection.commit()

    Now that we have brought the shapefile's contents into the database, we need to do
    something in PostGIS that isn't necessary with MySQL or SpatiaLite: we need to run
    a VACUUM ANALYZE command so that PostGIS can gather statistics to help it optimize
    our database queries:

    .. code-block:: python

        old_level = connection.isolation_level
        connection.set_isolation_level(0)
        cursor.execute("VACUUM ANALYZE")
        connection.set_isolation_level(old_level)

    We next want to search for the UK shoreline based upon the coordinate for
    London. This code is simpler than the MySQL version, thanks to the fact that
    PostGIS automatically does the bounding box check followed by the full
    polygon check, so we don't have to do this by hand:

    .. code-block:: python

        LONDON = 'POINT(-0.1263 51.4980)'

        cursor.execute("SELECT id,ST_AsText(geom) FROM gshhs " +
                        "WHERE (level=%s) AND " +
                        "(ST_Contains(geom, ST_GeomFromText(%s, 4326)))",
                        (1, LONDON))

        shoreline = None
        for id,wkt in cursor:
            shoreline = wkt

    Following the recommended best practices, we will ask PostGIS to tell us how it thinks this query will be performed:

    .. code-block:: python

        % usr/local/pgsql/bin/psql -U userName -d dbName
        psql> EXPLAIN SELECT id,ST_AsText(geom) FROM gshhs
        WHERE (level=2) AND (ST_Contains(geom,
        ST_GeomFromText('POINT(-0.1263 51.4980)', 4326)));

                                QUERY PLAN
        ---------------------------------------------------------
        Index Scan using geomindex on gshhs (cost=0.00..8.53 rows=1
        width=673)
        Index Cond: (geom && '0101000020E6100000ED0DBE30992AC0BF39B4C876BEB
            F4940'::geometry)
        Filter: ((level = 2) AND _st_contains(geom, '0101000020E6100000ED0D
            BE30992AC0BF39B4C876BEBF4940'::geometry))
        (3 rows)

    This tells us that PostGIS will answer this query by scanning through the geomindex
    spatial index, first filtering by bounding box (using the && operator), and then calling
    ST_Contains() to see if the polygon actually contains the desired point.

    This is exactly what we were hoping to see; the database is processing this query as
    quickly as possible while still giving us completely accurate results.

    Now that we have the desired shoreline polygon, let's finish our program by saving
    the polygon's WKT representation to disk:

    .. code-block:: python

        f = file("uk-shoreline.wkt", "w")
        f.write(shoreline)
        f.close()

    As with the MySQL version, running this program will create the uk-shoreline.wkt file containing the same low-resolution outline of the United Kingdom's shoreline.


使用 SpatiaLite
--------------------------------
Working with SpatiaLite

.. tab:: 中文

    Let's rewrite this program once more, this time to use SpatiaLite. As discussed earlier, we will create a database file and then call the InitSpatialMetaData() function. This will create and set up our spatial database.

    .. code-block:: python

        import os, os.path
        from pysqlite2 import dbapi2 as sqlite

        if os.path.exists("gshhs-spatialite.db"):
            os.remove("gshhs-spatialite.db")
        
        db = sqlite.connect("gshhs-spatialite.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("libspatialite.dll")')
        cursor = db.cursor()

        cursor.execute("SELECT InitSpatialMetaData()")

    .. note:: If you are running on Mac OS X, you can skip the db.enable_load_extension(...) and db.execute('SELECT load_extension(...)') statements.

    We next need to create our database table. This is done in almost exactly the same way as our PostGIS version:

    .. code-block:: python

        cursor.execute("DROP TABLE IF EXISTS gshhs")
        cursor.execute("CREATE TABLE gshhs (" +
                        "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                        "level INTEGER)")
        cursor.execute("CREATE INDEX gshhs_level on gshhs(level)")
        cursor.execute("SELECT AddGeometryColumn('gshhs', 'geom', " +
                        "4326, 'POLYGON', 2)")
        cursor.execute("SELECT CreateSpatialIndex('gshhs', 'geom')")
        db.commit()

    Loading the contents of the shapefile into the database is almost the same as the other versions of our program:

    .. code-block:: python

        import os.path
        from osgeo import ogr

        for level in [1, 2, 3, 4]:
            fName = os.path.join("GSHHS_l",
                "GSHHS_l_L"+str(level)+".shp")
            shapefile = ogr.Open(fName)
            layer = shapefile.GetLayer(0)
            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                geometry = feature.GetGeometryRef()
                wkt = geometry.ExportToWkt()

                cursor.execute("INSERT INTO gshhs (level, geom) " +
                               "VALUES (?, GeomFromText(?, 4326))",
                (level, wkt))
            db.commit()

    We've now reached the point where we want to search through the database for the desired polygon. Here is how we can do this in SpatiaLite:

    .. code-block:: python

        import shapely.wkt

        LONDON = 'POINT(-0.1263 51.4980)'

        pt = shapely.wkt.loads(LONDON)

        cursor.execute("SELECT id,level,AsText(geom) " +
                        "FROM gshhs WHERE id IN " +
                        "(SELECT pkid FROM idx_gshhs_geom" +
                        " WHERE xmin <= ? AND ? <= xmax" +
                        " AND ymin <= ? and ? <= ymax) " +
                        "AND Contains(geom, GeomFromText(?, 4326))",
                        (pt.x, pt.x, pt.y, pt.y, LONDON))

        shoreline = None
        for id,level,wkt in cursor:
            if level == 1:
                shoreline = wkt

    Because SpatiaLite's query optimizer doesn't use spatial indexes by default, we have
    to explicitly included the idx_gshhs_geom index in our query. Notice, however, that
    this time we aren't using Shapely to extract the polygon to see if the point is within
    it. Instead, we are using SpatiaLite's Contains() function directly to do the full
    polygon check directly within the query itself, after doing the bounding-box check
    using the spatial index.

    This query is complex, but in theory should produce a fast and accurate result.
    Following the recommended best practice, we want to check our query by asking
    SpatiaLite's query optimizer how the query will be processed. This will tell us if
    we have written the query correctly.

    Unfortunately, depending on how your copy of SpatiaLite was installed, you may
    not have access to the SQLite command line. So instead, let's call the EXPLAIN QUERY
    PLAN command from Python:

    .. code-block:: python

        cursor.execute("EXPLAIN QUERY PLAN " +
                       "SELECT id,level,AsText(geom) " +
                       "FROM gshhs WHERE id IN " +
                       "(SELECT pkid FROM idx_gshhs_geom" +
                       " WHERE xmin <= ? AND ? <= xmax" +
                       " AND ymin <= ? and ? <= ymax) " +
                       "AND Contains(geom, GeomFromText(?, 4326))",
                       (pt.x, pt.x, pt.y, pt.y, LONDON))
        for row in cursor:
            print row

    Running this tells us that the SpatiaLite query optimizer will use the spatial index
    (along with the table's primary key) to quickly identify the features that match by
    bounding box:

    .. code-block:: python

        (0, 0, 0, 'SEARCH TABLE gshhs USING PRIMARY KEY (rowid=?) (~12 rows)')
        (0, 0, 0, 'EXECUTE LIST SUBQUERY 1')
        (1, 0, 0, 'SCAN TABLE idx_gshhs_geom VIRTUAL TABLE INDEX 2:BaDbBcDd (~0
        rows)')

    .. note::

        Note that there is a bug in SpatiaLite that prevents it from using both a spatial index and an ordinary B*Tree index in the same query. This is why our Python program asks SpatiaLite to return the level value, and then checks for the level explicitly before identifying the shoreline, rather than simply embedding AND (level=1) in the query itself.

    Now that we have the shoreline, saving it to a text file is again trivial:

    .. code-block:: python

        f = file("uk-shoreline.wkt", "w")
        f.write(shoreline)
        f.close()

.. tab:: 英文

    Let's rewrite this program once more, this time to use SpatiaLite. As discussed earlier, we will create a database file and then call the InitSpatialMetaData() function. This will create and set up our spatial database.

    .. code-block:: python

        import os, os.path
        from pysqlite2 import dbapi2 as sqlite

        if os.path.exists("gshhs-spatialite.db"):
            os.remove("gshhs-spatialite.db")
        
        db = sqlite.connect("gshhs-spatialite.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("libspatialite.dll")')
        cursor = db.cursor()
        
        cursor.execute("SELECT InitSpatialMetaData()")

    .. note:: If you are running on Mac OS X, you can skip the db.enable_load_extension(...) and db.execute('SELECT load_extension(...)') statements.

    We next need to create our database table. This is done in almost exactly the same way as our PostGIS version:

    .. code-block:: python

        cursor.execute("DROP TABLE IF EXISTS gshhs")
        cursor.execute("CREATE TABLE gshhs (" +
                        "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                        "level INTEGER)")
        cursor.execute("CREATE INDEX gshhs_level on gshhs(level)")
        cursor.execute("SELECT AddGeometryColumn('gshhs', 'geom', " +
                        "4326, 'POLYGON', 2)")
        cursor.execute("SELECT CreateSpatialIndex('gshhs', 'geom')")
        db.commit()

    Loading the contents of the shapefile into the database is almost the same as the other versions of our program:

    .. code-block:: python

        import os.path
        from osgeo import ogr

        for level in [1, 2, 3, 4]:
            fName = os.path.join("GSHHS_l",
                "GSHHS_l_L"+str(level)+".shp")
            shapefile = ogr.Open(fName)
            layer = shapefile.GetLayer(0)
            for i in range(layer.GetFeatureCount()):
                feature = layer.GetFeature(i)
                geometry = feature.GetGeometryRef()
                wkt = geometry.ExportToWkt()

                cursor.execute("INSERT INTO gshhs (level, geom) " +
                               "VALUES (?, GeomFromText(?, 4326))",
                (level, wkt))
            db.commit()

    We've now reached the point where we want to search through the database for the desired polygon. Here is how we can do this in SpatiaLite:

    .. code-block:: python

        import shapely.wkt

        LONDON = 'POINT(-0.1263 51.4980)'

        pt = shapely.wkt.loads(LONDON)

        cursor.execute("SELECT id,level,AsText(geom) " +
                        "FROM gshhs WHERE id IN " +
                        "(SELECT pkid FROM idx_gshhs_geom" +
                        " WHERE xmin <= ? AND ? <= xmax" +
                        " AND ymin <= ? and ? <= ymax) " +
                        "AND Contains(geom, GeomFromText(?, 4326))",
                        (pt.x, pt.x, pt.y, pt.y, LONDON))

        shoreline = None
        for id,level,wkt in cursor:
            if level == 1:
                shoreline = wkt

    Because SpatiaLite's query optimizer doesn't use spatial indexes by default, we have
    to explicitly included the idx_gshhs_geom index in our query. Notice, however, that
    this time we aren't using Shapely to extract the polygon to see if the point is within
    it. Instead, we are using SpatiaLite's Contains() function directly to do the full
    polygon check directly within the query itself, after doing the bounding-box check
    using the spatial index.

    This query is complex, but in theory should produce a fast and accurate result.
    Following the recommended best practice, we want to check our query by asking
    SpatiaLite's query optimizer how the query will be processed. This will tell us if
    we have written the query correctly.

    Unfortunately, depending on how your copy of SpatiaLite was installed, you may
    not have access to the SQLite command line. So instead, let's call the EXPLAIN QUERY
    PLAN command from Python:

    .. code-block:: python

        cursor.execute("EXPLAIN QUERY PLAN " +
                       "SELECT id,level,AsText(geom) " +
                       "FROM gshhs WHERE id IN " +
                       "(SELECT pkid FROM idx_gshhs_geom" +
                       " WHERE xmin <= ? AND ? <= xmax" +
                       " AND ymin <= ? and ? <= ymax) " +
                       "AND Contains(geom, GeomFromText(?, 4326))",
                       (pt.x, pt.x, pt.y, pt.y, LONDON))
        for row in cursor:
            print row

    Running this tells us that the SpatiaLite query optimizer will use the spatial index
    (along with the table's primary key) to quickly identify the features that match by
    bounding box:

    .. code-block:: python

        (0, 0, 0, 'SEARCH TABLE gshhs USING PRIMARY KEY (rowid=?) (~12 rows)')
        (0, 0, 0, 'EXECUTE LIST SUBQUERY 1')
        (1, 0, 0, 'SCAN TABLE idx_gshhs_geom VIRTUAL TABLE INDEX 2:BaDbBcDd (~0
        rows)')

    .. note::

        Note that there is a bug in SpatiaLite that prevents it from using both a spatial index and an ordinary B*Tree index in the same query. This is why our Python program asks SpatiaLite to return the level value, and then checks for the level explicitly before identifying the shoreline, rather than simply embedding AND (level=1) in the query itself.

    Now that we have the shoreline, saving it to a text file is again trivial:

    .. code-block:: python

        f = file("uk-shoreline.wkt", "w")
        f.write(shoreline)
        f.close()


比较数据库
--------------------------------
Comparing the databases

.. tab:: 中文

    Now that we have seen how our program is implemented using each of the three open source spatial databases, we can start to draw some conclusions about these databases:

    - MySQL is easy to set up and use, is widely deployed, and can be used as a capable spatial database, though it does suffer from some limitations which require work-arounds.
    - PostGIS is the workhorse of open-source geospatial databases. It is fast and scales well, and has more capabilities than any of the other databases we have examined. At the same time, PostGIS has a reputation for being hard to set up and administer, and may be overkill for some applications.
    - SpatiaLite is fast and capable, though it is tricky to use well and has its fair share of quirks and bugs.

    Which database you choose to use, of course, depends on what you are trying to achieve, as well as factors such as which tools you have access to on your particular server, and your personal preference for which of these databases you want to work with. Whichever database you choose, you can be confident that it is more than capable of meeting your spatial database needs.

.. tab:: 英文

    Now that we have seen how our program is implemented using each of the three open source spatial databases, we can start to draw some conclusions about these databases:

    - MySQL is easy to set up and use, is widely deployed, and can be used as a capable spatial database, though it does suffer from some limitations which require work-arounds.
    - PostGIS is the workhorse of open-source geospatial databases. It is fast and scales well, and has more capabilities than any of the other databases we have examined. At the same time, PostGIS has a reputation for being hard to set up and administer, and may be overkill for some applications.
    - SpatiaLite is fast and capable, though it is tricky to use well and has its fair share of quirks and bugs.

    Which database you choose to use, of course, depends on what you are trying to achieve, as well as factors such as which tools you have access to on your particular server, and your personal preference for which of these databases you want to work with. Whichever database you choose, you can be confident that it is more than capable of meeting your spatial database needs.

