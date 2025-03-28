支持空间功能的开源数据库
============================================

Open source spatially-enabled databases

.. tab:: 中文

    If you wish to use an open source database for your geospatial development work, you currently have three main options: **MySQL**, **PostGIS** and **SpatiaLite**. Each has its own advantages and disadvantages, and no one database is the ideal choice in every situation. Let's take a closer look at each of these spatially-enabled databases.

.. tab:: 英文

    If you wish to use an open source database for your geospatial development work, you currently have three main options: **MySQL**, **PostGIS** and **SpatiaLite**. Each has its own advantages and disadvantages, and no one database is the ideal choice in every situation. Let's take a closer look at each of these spatially-enabled databases.

MySQL
-----------
MySQL

.. tab:: 中文

    MySQL is the world's most popular open source database, and is generally an extremely capable database. It is also spatially-enabled, though with some limitations, which we will get to in a moment.

    The MySQL database server can be downloaded from http://mysql.com/ downloads for a variety of operating systems, including MS Windows, Mac OS X, and Linux. Click on the MySQL Community Server link to download the server.

    Once downloaded, running the installer will set up everything you need, and you can access MySQL directly from the command line:

    .. code-block:: console

        % mysql
        Welcome to the MySQL monitor.
        Commands end with ; or \g.
        Your MySQL connection id is 14
        Server version: 5.5.28 MySQL Community Server (GPL)

        Copyright (c) 2000,2012, Oracle and/or its affiliates. All rights
        reserved.
        
        Oracle is a registered trademark of Oracle Corporation and/or its
        affiliates. Other names may be trademarks of their respective owners.
        
        Type 'help;' or '\h' for help. Type '\c' to clear the current input
        statement.
        
        mysql>

    To access MySQL from your Python programs, you need the MySQL-Python driver, which is available from http://sourceforge.net/projects/mysql-python. You can download the driver in source code format for Mac OS X and Linux, as well as MS Windows installers for Python version 2.7. If you need MS Windows installers for earlier versions of Python, these are available at http://www.codegood.com.

    The MySQL-Python driver acts as an interface between MySQL and your Python programs:

    .. image:: ./img/181-0.png
       :scale: 100
       :align: center

    Once you have installed the MySQL-Python driver, it will be available as a module named MySQLdb. Here is an example of how you might use this module from within your Python programs:

    .. code-block:: python

        import MySQLdb

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()
        cursor.execute("USE myDatabase")

    The cursor.execute() method lets you execute any MySQL command, just as if you were using the MySQL command-line client. MySQLdb is also completely compatible with the **Python Database API** specification (http://www.python.org/ dev/peps/pep-0249) and allows you to access all of MySQL's features from within your Python programs.

    Learning how to use databases within Python is beyond the scope of this book. If you haven't used a DB-API compatible database from Python before, you may want to check out one of the many available tutorials on the subject, for example: http://tutorialspoint.com/python/python_database_access.htm. Also, the Python Database Programming Wiki page (http://wiki.python.org/moin/ DatabaseProgramming) and the users guide for MySQLdb (http://mysql-python. sourceforge.net/MySQLdb.html) have useful information.

    MySQL comes with spatial capabilities built in. For example, the following MySQL command creates a new database table that contains a polygon:

    .. code-block:: sql

        CREATE TABLE cities (
            id      INTEGER AUTO_INCREMENT PRIMARY KEY,
            name    CHAR(255),
            outline POLYGON NOT NULL,
        
            INDEX (name),
            SPATIAL INDEX (outline)) ENGINE=MyISAM

    Note that you have to specify the MyISAM storage engine if you want to use spatial indexes. As of MySQL Version 5.5, the default storage engine changed from MyISAM to InnoDB, so you now need to specify the engine when creating your spatial database table.

    Notice that POLYGON is a valid column type, and that you can directly create a spatial index on a geometry. This allows you to issue queries such as:

    .. code-block:: sql

        SELECT name FROM cities WHERE MBRContains(outline, myLocation)

    The preceding query will return all the cities where the MBRContains() function determines that the given location is within the city's outline.

    This brings us to the first big disadvantage with using MySQL as a spatial database: the "MBR" at the start of the MBRContains() function stands for **Minimum Bounding Rectangle**. The MBRContains() function doesn't actually determine if the point is inside the polygon; rather, it determines if the point is inside the polygon's minimum bounding rectangle:

    .. image:: ./img/183-0.png
       :scale: 80
       :align: center
       :class: with-border

    As you can see, the dark points are inside the minimum bounding rectangle, while the lighter points are outside this rectangle. This means that the MBRContains() function returns false positives; that is, points that are inside the bounding rectangle, but outside the polygon itself.

    MySQL Version 5.6 will remove this limitation, though as of this writing Version 5.5 is the current stable release and Version 5.6 (and its associated Python drivers) may not be available for some time.

    Now, before you give up on MySQL completely, consider what this bounding-rectangle calculation gives you. If you have a million points and need to quickly determine which points are within a given polygon, the MBRContains() function will reduce that down to the small number of points that might be inside the polygon, by virtue of being in the polygon's bounding rectangle. You can then extract the polygon from the database and use another function such as Shapely's polygon.contains(point) method to do the final calculation on these few remaining points, like this:

    .. code-block:: python

        # Fetch the polygon we want to compare against:

        cursor.execute("SELECT AsText(outline) FROM cities WHERE...")
        wkt = cursor.fetchone()[0]

        polygon = shapely.wkt.loads(wkt)
        pointsInPolygon = []

        # Search for coordinates within the polygon's bounding rectangle:
        
        cursor.execute("SELECT X(coord),Y(coord) FROM coordinates " +
                       "WHERE MBRContains(GEOMFromText(%s), coord)",
                       (wkt,))
        for x,y in cursor:

            # See if the polygon actually contains this coordinate.
            
            point = shapely.geometry.Point(x, y)
            if polygon.contains(point):
                pointsInPolygon.append(point)

    As you can see, we first ask the database to find all points within the polygon's minimum bounding rectangle, and then check each returned point to see if it is actually inside the polygon. This approach is a bit more work, but it gets the job done and (for typical polygon shapes) will be extremely efficient and scalable.

    MySQL has other disadvantages as well—the range of spatial functions is more limited, and performance can sometimes be a problem—but it does have two major advantages which make it a serious contender for geospatial development:

    - MySQL is extremely popular, so if you are using a hosted server or have a computer set up for you, chances are that MySQL will already be installed. Hosting providers in particular may be very reluctant to install a different database server for you to use.
    - MySQL is the easiest database to install, set up, and administer. Other databases (in particular PostgreSQL) are often much more difficult to set up and use correctly.


.. tab:: 英文

    MySQL is the world's most popular open source database, and is generally an extremely capable database. It is also spatially-enabled, though with some limitations, which we will get to in a moment.

    The MySQL database server can be downloaded from http://mysql.com/ downloads for a variety of operating systems, including MS Windows, Mac OS X, and Linux. Click on the MySQL Community Server link to download the server.

    Once downloaded, running the installer will set up everything you need, and you can access MySQL directly from the command line:

    .. code-block:: console

        % mysql
        Welcome to the MySQL monitor.
        Commands end with ; or \g.
        Your MySQL connection id is 14
        Server version: 5.5.28 MySQL Community Server (GPL)

        Copyright (c) 2000,2012, Oracle and/or its affiliates. All rights
        reserved.
        
        Oracle is a registered trademark of Oracle Corporation and/or its
        affiliates. Other names may be trademarks of their respective owners.
        
        Type 'help;' or '\h' for help. Type '\c' to clear the current input
        statement.
        
        mysql>

    To access MySQL from your Python programs, you need the MySQL-Python driver, which is available from http://sourceforge.net/projects/mysql-python. You can download the driver in source code format for Mac OS X and Linux, as well as MS Windows installers for Python version 2.7. If you need MS Windows installers for earlier versions of Python, these are available at http://www.codegood.com.

    The MySQL-Python driver acts as an interface between MySQL and your Python programs:

    .. image:: ./img/181-0.png
       :scale: 100
       :align: center

    Once you have installed the MySQL-Python driver, it will be available as a module named MySQLdb. Here is an example of how you might use this module from within your Python programs:

    .. code-block:: python

        import MySQLdb

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()
        cursor.execute("USE myDatabase")

    The cursor.execute() method lets you execute any MySQL command, just as if you were using the MySQL command-line client. MySQLdb is also completely compatible with the **Python Database API** specification (http://www.python.org/ dev/peps/pep-0249) and allows you to access all of MySQL's features from within your Python programs.

    Learning how to use databases within Python is beyond the scope of this book. If you haven't used a DB-API compatible database from Python before, you may want to check out one of the many available tutorials on the subject, for example: http://tutorialspoint.com/python/python_database_access.htm. Also, the Python Database Programming Wiki page (http://wiki.python.org/moin/ DatabaseProgramming) and the users guide for MySQLdb (http://mysql-python. sourceforge.net/MySQLdb.html) have useful information.

    MySQL comes with spatial capabilities built in. For example, the following MySQL command creates a new database table that contains a polygon:

    .. code-block:: sql

        CREATE TABLE cities (
            id      INTEGER AUTO_INCREMENT PRIMARY KEY,
            name    CHAR(255),
            outline POLYGON NOT NULL,
        
            INDEX (name),
            SPATIAL INDEX (outline)) ENGINE=MyISAM

    Note that you have to specify the MyISAM storage engine if you want to use spatial indexes. As of MySQL Version 5.5, the default storage engine changed from MyISAM to InnoDB, so you now need to specify the engine when creating your spatial database table.

    Notice that POLYGON is a valid column type, and that you can directly create a spatial index on a geometry. This allows you to issue queries such as:

    .. code-block:: sql

        SELECT name FROM cities WHERE MBRContains(outline, myLocation)

    The preceding query will return all the cities where the MBRContains() function determines that the given location is within the city's outline.

    This brings us to the first big disadvantage with using MySQL as a spatial database: the "MBR" at the start of the MBRContains() function stands for **Minimum Bounding Rectangle**. The MBRContains() function doesn't actually determine if the point is inside the polygon; rather, it determines if the point is inside the polygon's minimum bounding rectangle:

    .. image:: ./img/183-0.png
       :scale: 80
       :align: center
       :class: with-border

    As you can see, the dark points are inside the minimum bounding rectangle, while the lighter points are outside this rectangle. This means that the MBRContains() function returns false positives; that is, points that are inside the bounding rectangle, but outside the polygon itself.

    MySQL Version 5.6 will remove this limitation, though as of this writing Version 5.5 is the current stable release and Version 5.6 (and its associated Python drivers) may not be available for some time.

    Now, before you give up on MySQL completely, consider what this bounding-rectangle calculation gives you. If you have a million points and need to quickly determine which points are within a given polygon, the MBRContains() function will reduce that down to the small number of points that might be inside the polygon, by virtue of being in the polygon's bounding rectangle. You can then extract the polygon from the database and use another function such as Shapely's polygon.contains(point) method to do the final calculation on these few remaining points, like this:

    .. code-block:: python

        # Fetch the polygon we want to compare against:

        cursor.execute("SELECT AsText(outline) FROM cities WHERE...")
        wkt = cursor.fetchone()[0]

        polygon = shapely.wkt.loads(wkt)
        pointsInPolygon = []

        # Search for coordinates within the polygon's bounding rectangle:
        
        cursor.execute("SELECT X(coord),Y(coord) FROM coordinates " +
                       "WHERE MBRContains(GEOMFromText(%s), coord)",
                       (wkt,))
        for x,y in cursor:

            # See if the polygon actually contains this coordinate.
            
            point = shapely.geometry.Point(x, y)
            if polygon.contains(point):
                pointsInPolygon.append(point)

    As you can see, we first ask the database to find all points within the polygon's minimum bounding rectangle, and then check each returned point to see if it is actually inside the polygon. This approach is a bit more work, but it gets the job done and (for typical polygon shapes) will be extremely efficient and scalable.

    MySQL has other disadvantages as well—the range of spatial functions is more limited, and performance can sometimes be a problem—but it does have two major advantages which make it a serious contender for geospatial development:

    - MySQL is extremely popular, so if you are using a hosted server or have a computer set up for you, chances are that MySQL will already be installed. Hosting providers in particular may be very reluctant to install a different database server for you to use.
    - MySQL is the easiest database to install, set up, and administer. Other databases (in particular PostgreSQL) are often much more difficult to set up and use correctly.

PostGIS
-----------
PostGIS

.. tab:: 中文

    **PostGIS** is an extension to the **PostgreSQL** database, allowing geospatial data to be stored in a PostgreSQL database. To use PostGIS from a Python application, you first have to install PostgreSQL, followed by the PostGIS extension, and finally the **Psycopg** database adapter so you can access PostgreSQL from Python. All this can get rather confusing:

    .. image:: ./img/185-0.png
       :scale: 100
       :align: center

.. tab:: 英文

    **PostGIS** is an extension to the **PostgreSQL** database, allowing geospatial data to be stored in a PostgreSQL database. To use PostGIS from a Python application, you first have to install PostgreSQL, followed by the PostGIS extension, and finally the **Psycopg** database adapter so you can access PostgreSQL from Python. All this can get rather confusing:

    .. image:: ./img/185-0.png
       :scale: 100
       :align: center

安装和配置 PostGIS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Installing and configuring PostGIS

.. tab:: 中文

    Let's take a look at what is required to use PostGIS on your computer:

    1. Install PostgreSQL:

       You first have to download and install the PostgreSQL database server.For MS Windows and Linux, installers can be found at:

       http://postgresql.org/download

       For Mac OS X, you should use the installer available at:

       http://kyngchaos.com/software/postgres

       Be warned that installing PostgreSQL can be complicated, and you may well need to configure or debug the server before it will work. The PostgreSQL documentation (http://postgresql.org/docs) can help, and remember that Google is your friend if you encounter any problems.
       
       .. note::

            Take note of where PostgreSQL has been installed on your computer. You will need to refer to files in the pgsql directory when you set up your spatially-enabled database.

    2. Install the PostGIS extension:

       The PostGIS spatial extension to PostgreSQL, along with full documentation, can be downloaded from:

       http://postgis.refractions.net

       Make sure you install the correct version of PostGIS to match the version of PostgreSQL you are using.

       .. note:: For Mac OS X, use the PostGIS installer available from the KyngChaos site.
    
    3. Install Psycopg:

       Psycopg allows you to access PostgreSQL (and PostGIS) databases from Python. The Psycopg database adapter can be found at:

       http://initd.org/psycopg

       Make sure you use Version 2 and not the outdated Version 1 of Psycopg. For Windows, you can download a prebuilt version of Psycopg; for Linux and Mac OS X, you need to download the source code and build it yourself in the usual way:

       .. code-block:: shell

            % cd psycopg2
            % python setup.py build
            % python setup.py install

       .. note:: Mac OS X users: If you are building Psycopg to run with the Kyngchaos version of PostgreSQL, type the following into the terminal window before you attempt to build Psycopg:

       .. code-block:: shell 

            % export PATH="/usr/local/pgsql/bin:$PATH"
            % export ARCHFLAGS="-arch i386"

    4. Setting up a New PostgreSQL user and database:

       Before you can use PostgreSQL, you need to have a user (sometimes called a "role" in the PostgreSQL manuals) that owns the database you create. While you might have a user account on your computer that you use for logging in and out, the PostgreSQL user is completely separate from this account, and is used only within PostgreSQL. You can set up a PostgreSQL user with the same name as your computer username, or you can give it a different name if you prefer.

       To create a new PostgreSQL user, type the following command:

       .. code-block:: shell 

            % pgsql/bin/createuser -s <username>

       .. note:: Obviously, replace <username> with whatever name you want to use for your new user. You may also need to change the path to the createuser command, if your PostgreSQL's bin directory isn't on your path. Finally, if you're running on a Mac, add -U postgres to the end of this command.

       Once you have set up a new PostgreSQL user, you can create a new database to work with:

       .. code-block:: shell 

            % pgsql/bin/createdb -U <username> <dbname>

       .. note:: Once again, replace <username> and <dbname> with the appropriate names for the user and database you wish to set up, and change the path to the createdb command if necessary.

       Note that we are keeping this as simple as possible. Setting up and administering a properly-configured PostgreSQL database is a major undertaking, and is way beyond the scope of this book. The preceding commands, however, should be enough to get you up and running.

    5. Spatially enable your new database:

       So far you have created a plain-vanilla PostgreSQL database. To turn this into a spatially-enabled database, you will need to configure the database to use PostGIS. Doing this is straightforward:

       .. code-block:: shell 

            % pgsql/bin/psql -d <dbname> -c "CREATE EXTENSION postgis;"

       After following these steps, you will have your own spatially-enabled PostGIS database. Let's now see how you can access this database from your Python programs.

.. tab:: 英文

使用 PostGIS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Using PostGIS

.. tab:: 中文

    Once you have installed the various pieces of software, and have set up a spatially-enabled database, you can use the Psycopg database adapter in the same way to how you would use MySQLdb to access a MySQL database:

    .. code-block:: python 

        import psycopg2

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()
        cursor.execute("SELECT id,name FROM cities WHERE pop>100000")
        
        for row in cursor:
            print row[0],row[1]

    Because *Psycopg* conforms to Python's DB-API specification, using PostgreSQL from Python is relatively straightforward, especially if you have used databases from Python before.

    Here is how you might create a new spatially-enabled table using PostGIS:   

    .. code-block:: python 

        import psycopg2
        
        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()
        
        cursor.execute("DROP TABLE IF EXISTS cities")
        cursor.execute("CREATE TABLE cities (id INTEGER," +
                       "name VARCHAR(255), PRIMARY KEY (id))")
        cursor.execute("SELECT AddGeometryColumn('cities', 'geom', " +
                       "-1, 'POLYGON', 2)")
        cursor.execute("CREATE INDEX cityIndex ON cities " +
                       "USING GIST (geom)")
        connection.commit()

    Let's take a look at each of these steps in more detail. We first get a cursor object to access the database, and then create the nonspatial parts of our table using standard SQL statements:

    .. code-block:: python 

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DROP TABLE IF EXISTS cities")
        cursor.execute("CREATE TABLE cities (id INTEGER," +
                        "name VARCHAR(255), PRIMARY KEY (id))")

    Once the table itself has been created, we have to use a separate PostGIS function called AddGeometryColumn() to define the spatial columns within our table:

    .. code-block:: python 

        cursor.execute("SELECT AddGeometryColumn('cities', 'geom', " + "-1, 'POLYGON', 2)")

    .. note::

        Recent versions of PostGIS support two distinct types of geospatial data, called **geometries** and **geographies**. The geometry type (which we are using here) uses Cartesian coordinates to place features onto a plane, and all calculations are done using Cartesian (x, y) coordinates. The geography type, on the other hand, identifies geospatial features using angular coordinates (latitude and longitude values) positioning the features onto a spheroid model of the Earth.

        The geography type is relatively new, much slower to use, and doesn't yet support all the functions that are available for the geometry type. Despite having the advantages of being able to accurately calculate distances which cover a large portion of the Earth and not requiring knowledge of projections and spatial references, we will not be using the geography type in this book.

        Finally, we create a spatial index so that we can efficiently search using the new geometry column:

    .. code-block:: python 

        cursor.execute("CREATE INDEX cityIndex ON cities " +
        "USING GIST (geom)")

    Once you have created your database, you can insert geometry features into it using the ST_GeomFromText() function, like this:

    .. code-block:: python 

        cursor.execute("INSERT INTO cities (name,geom) VALUES " +
        "(%s, ST_GeomFromText(%s)", (cityName, wkt))

    Conversely, you can retrieve a geometry from the database in WKT format using the ST_AsText() function:

    .. code-block:: python 

        cursor.execute("select name,ST_AsText(geom) FROM cities")
        for name,wkt in cursor:

.. tab:: 英文

    Once you have installed the various pieces of software, and have set up a spatially-enabled database, you can use the Psycopg database adapter in the same way to how you would use MySQLdb to access a MySQL database:

    .. code-block:: python 

        import psycopg2

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()
        cursor.execute("SELECT id,name FROM cities WHERE pop>100000")
        
        for row in cursor:
            print row[0],row[1]

    Because *Psycopg* conforms to Python's DB-API specification, using PostgreSQL from Python is relatively straightforward, especially if you have used databases from Python before.

    Here is how you might create a new spatially-enabled table using PostGIS:   

    .. code-block:: python 

        import psycopg2
        
        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()
        
        cursor.execute("DROP TABLE IF EXISTS cities")
        cursor.execute("CREATE TABLE cities (id INTEGER," +
                       "name VARCHAR(255), PRIMARY KEY (id))")
        cursor.execute("SELECT AddGeometryColumn('cities', 'geom', " +
                       "-1, 'POLYGON', 2)")
        cursor.execute("CREATE INDEX cityIndex ON cities " +
                       "USING GIST (geom)")
        connection.commit()

    Let's take a look at each of these steps in more detail. We first get a cursor object to access the database, and then create the nonspatial parts of our table using standard SQL statements:

    .. code-block:: python 

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DROP TABLE IF EXISTS cities")
        cursor.execute("CREATE TABLE cities (id INTEGER," +
                        "name VARCHAR(255), PRIMARY KEY (id))")

    Once the table itself has been created, we have to use a separate PostGIS function called AddGeometryColumn() to define the spatial columns within our table:

    .. code-block:: python 

        cursor.execute("SELECT AddGeometryColumn('cities', 'geom', " + "-1, 'POLYGON', 2)")

    .. note::

        Recent versions of PostGIS support two distinct types of geospatial data, called **geometries** and **geographies**. The geometry type (which we are using here) uses Cartesian coordinates to place features onto a plane, and all calculations are done using Cartesian (x, y) coordinates. The geography type, on the other hand, identifies geospatial features using angular coordinates (latitude and longitude values) positioning the features onto a spheroid model of the Earth.

        The geography type is relatively new, much slower to use, and doesn't yet support all the functions that are available for the geometry type. Despite having the advantages of being able to accurately calculate distances which cover a large portion of the Earth and not requiring knowledge of projections and spatial references, we will not be using the geography type in this book.

        Finally, we create a spatial index so that we can efficiently search using the new geometry column:

    .. code-block:: python 

        cursor.execute("CREATE INDEX cityIndex ON cities " +
        "USING GIST (geom)")

    Once you have created your database, you can insert geometry features into it using the ST_GeomFromText() function, like this:

    .. code-block:: python 

        cursor.execute("INSERT INTO cities (name,geom) VALUES " +
        "(%s, ST_GeomFromText(%s)", (cityName, wkt))

    Conversely, you can retrieve a geometry from the database in WKT format using the ST_AsText() function:

    .. code-block:: python 

        cursor.execute("select name,ST_AsText(geom) FROM cities")
        for name,wkt in cursor:

文档
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Documentation

.. tab:: 中文

    Because PostGIS is an extension to PostgreSQL, and you use Psycopg to access it, there are three separate sets of documentation that you will need to refer to:

    - The PostgreSQL manual: http://postgresql.org/docs
    - The PostGIS manual: http://postgis.refractions.net/docs
    - The Psycopg documentation: http://initd.org/psycopg/docs

    Of these, the PostGIS manual is probably going to be the most useful, and you will also need to refer to the Psycopg documentation to find out the details of using PostGIS from Python. You will probably also need to refer to the PostgreSQL manual to learn the nonspatial aspects of using PostGIS, though be aware that this manual is huge and extremely complex, and reflects the complexity of PostgreSQL itself.

.. tab:: 英文

    Because PostGIS is an extension to PostgreSQL, and you use Psycopg to access it, there are three separate sets of documentation that you will need to refer to:

    - The PostgreSQL manual: http://postgresql.org/docs
    - The PostGIS manual: http://postgis.refractions.net/docs
    - The Psycopg documentation: http://initd.org/psycopg/docs

    Of these, the PostGIS manual is probably going to be the most useful, and you will also need to refer to the Psycopg documentation to find out the details of using PostGIS from Python. You will probably also need to refer to the PostgreSQL manual to learn the nonspatial aspects of using PostGIS, though be aware that this manual is huge and extremely complex, and reflects the complexity of PostgreSQL itself.

高级 PostGIS 功能
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Advanced PostGIS Features

.. tab:: 中文

    PostGIS supports the following features that not available with MySQL:

    - On-the-fly transformations of geometries from one spatial reference to another.
    - The ability to edit geometries by adding, changing, and removing points, and by rotating, scaling, and shifting entire geometries.
    - The ability to read and write geometries in GeoJSON, GML, KML, and SVG formats, in addition to WKT and WKB.
    - A complete range of bounding-box comparisons, including A overlaps B, A contains B, A is to the left of B, and so on. These comparison operators make use of spatial indexes to identify matching features extremely quickly.
    - Proper spatial comparisons between geometries, including intersection, containment, crossing, equality, overlap, touching, and so on. These comparisons are done using the true geometry rather than just their bounding boxes.
    - Spatial functions to calculate information such as the area, centroid, closest point, distance, length, perimeter, shortest connecting line, and so on. These functions take into account the geometry's spatial reference, if known.
    - Support for both vector and raster format geospatial data.
    - An optional **geocoder** based on TIGER/Line data, allowing you to convert from street addresses to a list of matching locations (US addresses only).

    PostGIS has a reputation for being a geospatial powerhouse. While it is not the only option for storing geospatial data, and is certainly the most complex database discussed in this book, it is worth considering if you are looking for a powerful spatially-enabled database to use from within your Python geospatial programs and can deal with the complexity of setting up and administering a PostgreSQL database.

.. tab:: 英文

    PostGIS supports the following features that not available with MySQL:

    - On-the-fly transformations of geometries from one spatial reference to another.
    - The ability to edit geometries by adding, changing, and removing points, and by rotating, scaling, and shifting entire geometries.
    - The ability to read and write geometries in GeoJSON, GML, KML, and SVG formats, in addition to WKT and WKB.
    - A complete range of bounding-box comparisons, including A overlaps B, A contains B, A is to the left of B, and so on. These comparison operators make use of spatial indexes to identify matching features extremely quickly.
    - Proper spatial comparisons between geometries, including intersection, containment, crossing, equality, overlap, touching, and so on. These comparisons are done using the true geometry rather than just their bounding boxes.
    - Spatial functions to calculate information such as the area, centroid, closest point, distance, length, perimeter, shortest connecting line, and so on. These functions take into account the geometry's spatial reference, if known.
    - Support for both vector and raster format geospatial data.
    - An optional **geocoder** based on TIGER/Line data, allowing you to convert from street addresses to a list of matching locations (US addresses only).

    PostGIS has a reputation for being a geospatial powerhouse. While it is not the only option for storing geospatial data, and is certainly the most complex database discussed in this book, it is worth considering if you are looking for a powerful spatially-enabled database to use from within your Python geospatial programs and can deal with the complexity of setting up and administering a PostgreSQL database.

SpatiaLite
-----------
SpatiaLite

.. tab:: 中文

    As the name suggests, SpatiaLite is a "lightweight" spatial database, though the performance is surprisingly good and it doesn't skimp on features. Just like PostGIS is a spatial extension to PostgreSQL, SpatiaLite is a spatial extension to the serverless SQLite database engine. To access SQLite (and SpatiaLite) from Python, you need to use the pysqlite database adapter:

    .. image:: ./img/191-0.png
       :scale: 100
       :align: center

.. tab:: 英文

    As the name suggests, SpatiaLite is a "lightweight" spatial database, though the performance is surprisingly good and it doesn't skimp on features. Just like PostGIS is a spatial extension to PostgreSQL, SpatiaLite is a spatial extension to the serverless SQLite database engine. To access SQLite (and SpatiaLite) from Python, you need to use the pysqlite database adapter:

    .. image:: ./img/191-0.png
       :scale: 100
       :align: center

安装 SpatiaLite
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Installing SpatiaLite

.. tab:: 中文

    Before you can use SpatiaLite in your Python programs, you need to install SQLite, SpatiaLite, and pysqlite. How you do this depends on which operating system your computer is running.

    - **Mac OS X**
        If you're using a Mac OS X-based system, you're in luck. The framework build of sqlite3 can be downloaded from:

        http://www.kyngchaos.com/software/frameworks

        This will install everything you need, and you won't have to deal with any configuration issues at all.

    - **MS Windows**
        For MS Windows based systems, you can download binary installers from the following site:

        http://gaia-gis.it/gaia-sins

        Near the bottom of this page is the MS Windows Binaries section, where you can download the appropriate installer.

    - **Linux**
        For Linux, you can download the source code to libspatialite from the SpatiaLite website:

        https://www.gaia-gis.it/fossil/libspatialite/index

        You can then follow the build instructions to compile libspatialite yourself.

.. tab:: 英文

    Before you can use SpatiaLite in your Python programs, you need to install SQLite, SpatiaLite, and pysqlite. How you do this depends on which operating system your computer is running.

    - **Mac OS X**
        If you're using a Mac OS X-based system, you're in luck. The framework build of sqlite3 can be downloaded from:

        http://www.kyngchaos.com/software/frameworks

        This will install everything you need, and you won't have to deal with any configuration issues at all.

    - **MS Windows**
        For MS Windows based systems, you can download binary installers from the following site:

        http://gaia-gis.it/gaia-sins

        Near the bottom of this page is the MS Windows Binaries section, where you can download the appropriate installer.

    - **Linux**
        For Linux, you can download the source code to libspatialite from the SpatiaLite website:

        https://www.gaia-gis.it/fossil/libspatialite/index

        You can then follow the build instructions to compile libspatialite yourself.

安装 pysqlite
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Installing pysqlite

.. tab:: 中文

    After installing the libspatialite library and its dependencies, you'll need to make sure you have a workable version of pysqlite, the Python database adapter for SQLite.

    .. note:: Mac users are once again in luck; the sqlite3 framework you downloaded already includes a suitable version of pysqlite, so you can ignore this section.

    A version of pysqlite comes bundled with Python Version 2.5 and later, in the form of a standard library module named sqlite3. This standard library module, however, may not work with SpatiaLite. Because SpatiaLite is an extension to SQLite, the pysqlite library must be able to load extensions—a feature that was only introduced in pysqlite Version 2.5, and is often disabled by default. To see if your version of Python includes a usable version of sqlite3, type the following into the Python command line:

    >>> import sqlite3
    >>> conn = sqlite3.connect(":memory:")
    >>> conn.enable_load_extension(True)

    If you get an AttributeError, your built-in version of sqlite3 does not support loading extensions, and you will have to download and install a different version. The main website for pysqlite is:

    http://code.google.com/p/pysqlite

    You can download binary versions for MS Windows, and source code packages for Linux, which you can compile yourself.

.. tab:: 英文

    After installing the libspatialite library and its dependencies, you'll need to make sure you have a workable version of pysqlite, the Python database adapter for SQLite.

    .. note:: Mac users are once again in luck; the sqlite3 framework you downloaded already includes a suitable version of pysqlite, so you can ignore this section.

    A version of pysqlite comes bundled with Python Version 2.5 and later, in the form of a standard library module named sqlite3. This standard library module, however, may not work with SpatiaLite. Because SpatiaLite is an extension to SQLite, the pysqlite library must be able to load extensions—a feature that was only introduced in pysqlite Version 2.5, and is often disabled by default. To see if your version of Python includes a usable version of sqlite3, type the following into the Python command line:

    >>> import sqlite3
    >>> conn = sqlite3.connect(":memory:")
    >>> conn.enable_load_extension(True)

    If you get an AttributeError, your built-in version of sqlite3 does not support loading extensions, and you will have to download and install a different version. The main website for pysqlite is:

    http://code.google.com/p/pysqlite

    You can download binary versions for MS Windows, and source code packages for Linux, which you can compile yourself.

从 Python 访问 SpatiaLite
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Accessing SpatiaLite from Python

.. tab:: 中文

    Now that you have all the libraries installed, you are ready to start using pysqlite to access and work with SpatiaLite databases. There is, however, one final thing to be aware of; because pysqlite is a database adapter for SQLite rather than SpatiaLite, you will need to load the libspatialite extension before you can use any of the SpatiaLite functionality in your Python program.

    .. note::

        Mac users don't need to do this, because the version of sqlite3 you downloaded comes with the libspatialite extension built in.

        If you are running on MS Windows, you may need to copy the SpatiaLite DLLs into the SYSTEM32 folder, or add the folder containing the SpatiaLite DLLs to the system path.

    To load the libspatialite extension, add the following highlighted statements to your Python program:

    .. code-block:: python

        from pysqlite2 import dbapi as sqlite
        
        conn = sqlite.connect("...")
        conn.enable_load_extension(True)
        conn.execute('SELECT load_extension("libspatialite-2.dll")')
        curs = conn.cursor()

    For Linux users, make sure you use the correct name for the libspatialite extension. You may also need to change the name of the pysqlite2 module you're importing depending on which version you downloaded.

.. tab:: 英文

    Now that you have all the libraries installed, you are ready to start using pysqlite to access and work with SpatiaLite databases. There is, however, one final thing to be aware of; because pysqlite is a database adapter for SQLite rather than SpatiaLite, you will need to load the libspatialite extension before you can use any of the SpatiaLite functionality in your Python program.

    .. note::

        Mac users don't need to do this, because the version of sqlite3 you downloaded comes with the libspatialite extension built in.

        If you are running on MS Windows, you may need to copy the SpatiaLite DLLs into the SYSTEM32 folder, or add the folder containing the SpatiaLite DLLs to the system path.

    To load the libspatialite extension, add the following highlighted statements to your Python program:

    .. code-block:: python

        from pysqlite2 import dbapi as sqlite
        
        conn = sqlite.connect("...")
        conn.enable_load_extension(True)
        conn.execute('SELECT load_extension("libspatialite-2.dll")')
        curs = conn.cursor()

    For Linux users, make sure you use the correct name for the libspatialite extension. You may also need to change the name of the pysqlite2 module you're importing depending on which version you downloaded.

文档
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Documentation

.. tab:: 中文

    With all these different packages, it can be quite confusing knowing where to look for more information. First off, you can learn more about the SQL syntax supported by SQLite (and SpatiaLite) by looking at the SQL as Understood by SQLite page:

    http://sqlite.org/lang.html

    Then, to learn more about SpatiaLite itself, check out the main SpatiaLite web page:

    https://www.gaia-gis.it/fossil/libspatialite/index

    You can access the SpatiaLite online documentation, as well as read through various tutorials, though these aren't Python-specific.

    Finally, to learn more about using pysqlite to access SQLite and SpatiaLite from Python, see:

    http://pysqlite.googlecode.com/svn/doc/sqlite3.html

.. tab:: 英文

    With all these different packages, it can be quite confusing knowing where to look for more information. First off, you can learn more about the SQL syntax supported by SQLite (and SpatiaLite) by looking at the SQL as Understood by SQLite page:

    http://sqlite.org/lang.html

    Then, to learn more about SpatiaLite itself, check out the main SpatiaLite web page:

    https://www.gaia-gis.it/fossil/libspatialite/index

    You can access the SpatiaLite online documentation, as well as read through various tutorials, though these aren't Python-specific.

    Finally, to learn more about using pysqlite to access SQLite and SpatiaLite from Python, see:

    http://pysqlite.googlecode.com/svn/doc/sqlite3.html

使用 SpatiaLite
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Using SpatiaLite

.. tab:: 中文

    In many ways, SpatiaLite has been modeled after PostGIS. Before using SpatiaLite for your database, you need to initialize SpatiaLite's internal metadata tables. You also need to explicitly define your spatial columns by calling the AddGeometryColumn() function, just like you do in PostGIS. Let's see how all this works by creating a SpatiaLite database and creating an example database table.

    As described earlier, the first step in using SpatiaLite is to connect to the database and load the SpatiaLite extension, like this:

    .. code-block:: python

        from pysqlite2 import dbapi2 as sqlite

        db = sqlite.connect("myDatabase.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("libspatialite.dll")')
        cursor = db.cursor()

    Note that because SQLite is a serverless database, the *myDatabase.db* database
    is simply a file on your hard disk. Also, if you are running on Mac OS X, you can
    skip the *enable_load_extension/SELECT load_extension* dance and remove
    or comment out these two lines.

    You next need to initialize the SpatiaLite metadata tables in your database.
    In previous versions of SpatiaLite, you had to import these tables by hand.
    It's now much easier—simply execute the following within your Python script:

    .. code-block:: python

        cursor.execute('SELECT InitSpatialMetaData()')

    .. note:: 
        
        If the metadata tables already exist, InitSpatialMetaData() will do nothing. This means you can safely call this function whenever you open the database, regardless of whether or not the database has already been initialized.

    After initializing the metadata, you can create your own database table to hold
    your geospatial data. As with PostGIS, this is a two-step process; you first create
    the nonspatial parts of your table using standard SQL statements:

    .. code-block:: python

        cursor.execute("DROP TABLE IF EXISTS cities")
        cursor.execute("CREATE TABLE cities (" +
                       "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                       "name CHAR(255))")

    You then use the SpatiaLite function AddGeometryColumn() to define the spatial column(s) in your table:

    .. code-block:: python

        cursor.execute("SELECT AddGeometryColumn('cities', 'geom', " +
                       "4326, 'POLYGON', 2)")

    .. note::
        
        The number 4326 is the spatial reference ID (SRID) used to identify the spatial reference this column's features will use. The SRID number 4326 refers to a spatial reference using latitude and longitude values and the WGS84 datum; we will look at SRID values in more detail in the Recommended Best Practices section.

    You can then create a spatial index on your geometries using the CreateSpatialIndex() function, like this:

    .. code-block:: python

        cursor.execute("SELECT CreateSpatialIndex('cities', 'geom')")

    Now that you have set up your database table, you can insert geometry features into it using the GeomFromText() function:

    .. code-block:: python

        cursor.execute("INSERT INTO cities (name, geom)" +
                       " VALUES (?, GeomFromText(?, 4326))",
                       (city, wkt))

    And you can retrieve geometries from the database in WKT format using the AsText() function:

    .. code-block:: python

        cursor.execute("SELECT name,AsText(geom) FROM cities")
        for name,wkt in cursor:

.. tab:: 英文

    In many ways, SpatiaLite has been modeled after PostGIS. Before using SpatiaLite for your database, you need to initialize SpatiaLite's internal metadata tables. You also need to explicitly define your spatial columns by calling the AddGeometryColumn() function, just like you do in PostGIS. Let's see how all this works by creating a SpatiaLite database and creating an example database table.

    As described earlier, the first step in using SpatiaLite is to connect to the database and load the SpatiaLite extension, like this:

    .. code-block:: python

        from pysqlite2 import dbapi2 as sqlite

        db = sqlite.connect("myDatabase.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("libspatialite.dll")')
        cursor = db.cursor()

    Note that because SQLite is a serverless database, the *myDatabase.db* database
    is simply a file on your hard disk. Also, if you are running on Mac OS X, you can
    skip the *enable_load_extension/SELECT load_extension* dance and remove
    or comment out these two lines.

    You next need to initialize the SpatiaLite metadata tables in your database.
    In previous versions of SpatiaLite, you had to import these tables by hand.
    It's now much easier—simply execute the following within your Python script:

    .. code-block:: python

        cursor.execute('SELECT InitSpatialMetaData()')

    .. note:: 
        
        If the metadata tables already exist, InitSpatialMetaData() will do nothing. This means you can safely call this function whenever you open the database, regardless of whether or not the database has already been initialized.

    After initializing the metadata, you can create your own database table to hold
    your geospatial data. As with PostGIS, this is a two-step process; you first create
    the nonspatial parts of your table using standard SQL statements:

    .. code-block:: python

        cursor.execute("DROP TABLE IF EXISTS cities")
        cursor.execute("CREATE TABLE cities (" +
                       "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                       "name CHAR(255))")

    You then use the SpatiaLite function AddGeometryColumn() to define the spatial column(s) in your table:

    .. code-block:: python

        cursor.execute("SELECT AddGeometryColumn('cities', 'geom', " +
                       "4326, 'POLYGON', 2)")

    .. note::
        
        The number 4326 is the spatial reference ID (SRID) used to identify the spatial reference this column's features will use. The SRID number 4326 refers to a spatial reference using latitude and longitude values and the WGS84 datum; we will look at SRID values in more detail in the Recommended Best Practices section.

    You can then create a spatial index on your geometries using the CreateSpatialIndex() function, like this:

    .. code-block:: python

        cursor.execute("SELECT CreateSpatialIndex('cities', 'geom')")

    Now that you have set up your database table, you can insert geometry features into it using the GeomFromText() function:

    .. code-block:: python

        cursor.execute("INSERT INTO cities (name, geom)" +
                    " VALUES (?, GeomFromText(?, 4326))",
                    (city, wkt))

    And you can retrieve geometries from the database in WKT format using the AsText() function:

    .. code-block:: python

        cursor.execute("SELECT name,AsText(geom) FROM cities")
        for name,wkt in cursor:

SpatiaLite 功能
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SpatiaLite capabilities

.. tab:: 中文

    Some highlights of SpatiaLite include:

    - The ability to handle all the major geometry types, including **Point, LineString, Polygon, MultiPoint, MultiLineString, MultiPolygon** and **GeometryCollection**.
    - Experimental support for topology-based datatypes (nodes, edges, faces, and so on) as an alternative to the above geometry types.
    - Every geometry feature has a spatial reference identifier (SRID) which tells you the spatial reference used by this feature.
    - Geometry columns are constrained to a particular type of geometry and a particular SRID. This prevents you from accidentally storing the wrong type of geometry, or a geometry with the wrong spatial reference, in a database table.
    - Support for translating geometries to and from various microformats, including WKT, WKB , GML, KML, and GeoJSON.
    - Support for geometry functions to do things such as calculate the area of a polygon, to simplify polygons and linestrings, to calculate the distance between two geometries, to calculate intersections, differences, and buffers.
    - Functions to transform geometries from one spatial reference to another, and to shift, scale, and rotate geometries.
    - Support for fast spatial relationship calculations using minimum bounding rectangles.
    - Support for complete spatial relationship calculations (equals, touches, intersects, and so on) using the geometry itself rather than just the bounding rectangle.
    - The use of R-Tree indexes, which can (if you use them correctly) produce impressive results when performing spatial queries. Calculating the intersection of 500,000 linestrings with 380,000 polygons took just nine seconds, according to one researcher.
    - An alternative way of implementing spatial indexes, using in-memory MBR caching. This can be an extremely fast way of indexing features using minimum bounding rectangles, though it is limited by the amount of available RAM and so isn't suitable for extremely large datasets.

    While SpatiaLite is considered to be a lightweight database, it is indeed surprisingly capable. Depending on your application, SpatiaLite may well be an excellent choice for your Python geospatial programming needs.

.. tab:: 英文

    Some highlights of SpatiaLite include:

    - The ability to handle all the major geometry types, including **Point, LineString, Polygon, MultiPoint, MultiLineString, MultiPolygon** and **GeometryCollection**.
    - Experimental support for topology-based datatypes (nodes, edges, faces, and so on) as an alternative to the above geometry types.
    - Every geometry feature has a spatial reference identifier (SRID) which tells you the spatial reference used by this feature.
    - Geometry columns are constrained to a particular type of geometry and a particular SRID. This prevents you from accidentally storing the wrong type of geometry, or a geometry with the wrong spatial reference, in a database table.
    - Support for translating geometries to and from various microformats, including WKT, WKB , GML, KML, and GeoJSON.
    - Support for geometry functions to do things such as calculate the area of a polygon, to simplify polygons and linestrings, to calculate the distance between two geometries, to calculate intersections, differences, and buffers.
    - Functions to transform geometries from one spatial reference to another, and to shift, scale, and rotate geometries.
    - Support for fast spatial relationship calculations using minimum bounding rectangles.
    - Support for complete spatial relationship calculations (equals, touches, intersects, and so on) using the geometry itself rather than just the bounding rectangle.
    - The use of R-Tree indexes, which can (if you use them correctly) produce impressive results when performing spatial queries. Calculating the intersection of 500,000 linestrings with 380,000 polygons took just nine seconds, according to one researcher.
    - An alternative way of implementing spatial indexes, using in-memory MBR caching. This can be an extremely fast way of indexing features using minimum bounding rectangles, though it is limited by the amount of available RAM and so isn't suitable for extremely large datasets.

    While SpatiaLite is considered to be a lightweight database, it is indeed surprisingly capable. Depending on your application, SpatiaLite may well be an excellent choice for your Python geospatial programming needs.
