设计和构建数据库
============================================

Designing and building the database

.. tab:: 中文

    Let's start our design of the DISTAL application by thinking about the various pieces of data it will require:

    - A list of all the countries. Each country needs to include a simple boundary map which can be displayed to the user.
    - Detailed shoreline and lake boundaries worldwide.
    - A list of all major cities and towns worldwide. For each city/town, we need to have the name of the city/town and a point representing the location of that town or city. 

    Fortunately, this data is readily available:

    - The list of countries and their outlines are included in the World Borders Dataset.
    - Shoreline and lake boundaries (as well as other land-water boundaries such as islands within lakes) are readily available using the GSHHS shoreline database.

      City and town data can be found in two places: The GNIS Database (http://geonames.usgs.gov/domestic) provides official place-name data for the United States, while the GEOnet Names Server (http://earth-info. nga.mil/gns/html) provides similar data for the rest of the world.

    Looking at these data sources, we can start to design the database schema for the DISTAL system:

    .. image:: ./img/228-0.png
       :align: center
       :class: with-border
       

    .. note::

        The level field in the shorelines table corresponds to the level value in the GSHHS database: a value of 1 represents a coastline, 2 represents a lake, 3 represents an island within a lake, and 4 represents a pond on an island in a lake.

    While this is very simple, it's enough to get us started. Let's use this schema to create our database, firstly in MySQL:

    .. code-block:: python

        import MySQLdb

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()

        cursor.execute("DROP DATABASE IF EXISTS distal")
        cursor.execute("CREATE DATABASE distal")
        cursor.execute("USE distal")
        cursor.execute("""
            CREATE TABLE countries (
                id       INTEGER AUTO_INCREMENT PRIMARY KEY,
                name     CHAR(255) CHARACTER SET utf8 NOT NULL,
                outline  GEOMETRY NOT NULL,
                
                SPATIAL INDEX (outline)) ENGINE=MyISAM
        """)

        cursor.execute("""
            CREATE TABLE shorelines (
                id      INTEGER AUTO_INCREMENT PRIMARY KEY,
                level   INTEGER NOT NULL,
                outline GEOMETRY NOT NULL,

                SPATIAL INDEX (outline)) ENGINE=MyISAM
        """)

        cursor.execute("""
            CREATE TABLE places (
                id       INTEGER AUTO_INCREMENT PRIMARY KEY,
                name     CHAR(255) CHARACTER SET utf8 NOT NULL,
                position POINT NOT NULL,

                SPATIAL INDEX (position)) ENGINE=MyISAM
        """)

        connection.commit()

    .. note:: 
        
        Note that we define the countries and places name fields to use UTF-8 character encoding. This allows us to store non-English names into these fields.

    The same code in PostGIS would look like this:

    .. code-block:: python

        import psycopg2

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DROP TABLE IF EXISTS countries")
        cursor.execute("""
            CREATE TABLE countries (
                        id
                        SERIAL,
                        name VARCHAR(255),

                        PRIMARY KEY (id))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('countries', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            CREATE INDEX countryIndex ON countries
                USING GIST(outline)
        """)

        cursor.execute("DROP TABLE IF EXISTS shorelines")
        cursor.execute("""
            CREATE TABLE shorelines (
                id    SERIAL,
                level INTEGER,

                PRIMARY KEY (id))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('shorelines', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            CREATE INDEX shorelineIndex ON shorelines
                USING GIST(outline)
        """)

        cursor.execute("DROP TABLE IF EXISTS places")
        cursor.execute("""
            CREATE TABLE places (
                id   SERIAL,
                name VARCHAR(255),

                PRIMARY KEY (id))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('places', 'position',
                                      4326, 'POINT', 2)
        """)

        cursor.execute("""
            CREATE INDEX placeIndex ON places
                USING GIST(position)
        """)

        connection.commit()

    .. note::

        Note how the PostGIS version allows us to specify the SRID value for the geometry columns. We'll be using the WG84 datum and unprojected lat/long coordinates for all our spatial data, which is why we specified SRID 4326 when we created our geometries.

    And finally, using SpatiaLite:

    .. code-block:: python

        import os, os.path
        from pysqlite2 import dbapi2 as sqlite

        if os.path.exists("distal.db"):
            os.remove("distal.db")
        
        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()
        
        # Initialize the SpatiaLite meta-tables.
        
        cursor.execute('SELECT InitSpatialMetaData()')
        
        # Create the database tables.
        
        cursor.execute("DROP TABLE IF EXISTS countries")
        cursor.execute("""
            CREATE TABLE countries (
                id   INTEGER PRIMARY KEY AUTOINCREMENT,
                name CHAR(255))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('countries', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            SELECT CreateSpatialIndex('countries', 'outline')
        """)

        cursor.execute("DROP TABLE IF EXISTS shorelines")
        cursor.execute("""
            CREATE TABLE shorelines (
                id    INTEGER PRIMARY KEY AUTOINCREMENT,
                level INTEGER)
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('shorelines', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            SELECT CreateSpatialIndex('shorelines', 'outline')
        """)

        cursor.execute("DROP TABLE IF EXISTS places")
        cursor.execute("""
            CREATE TABLE places (
                id   INTEGER PRIMARY KEY AUTOINCREMENT,
                name CHAR(255))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('places', 'position',
                                      4326, 'POINT', 2)
        """)

        cursor.execute("""
            SELECT CreateSpatialIndex('places', 'position')
        """)
        db.commit()
    
    Now that we've set up our database, let's get the data we need for the DISTAL application.

.. tab:: 英文

    Let's start our design of the DISTAL application by thinking about the various pieces of data it will require:

    - A list of all the countries. Each country needs to include a simple boundary map which can be displayed to the user.
    - Detailed shoreline and lake boundaries worldwide.
    - A list of all major cities and towns worldwide. For each city/town, we need to have the name of the city/town and a point representing the location of that town or city. 

    Fortunately, this data is readily available:

    - The list of countries and their outlines are included in the World Borders Dataset.
    - Shoreline and lake boundaries (as well as other land-water boundaries such as islands within lakes) are readily available using the GSHHS shoreline database.

      City and town data can be found in two places: The GNIS Database (http://geonames.usgs.gov/domestic) provides official place-name data for the United States, while the GEOnet Names Server (http://earth-info. nga.mil/gns/html) provides similar data for the rest of the world.

    Looking at these data sources, we can start to design the database schema for the DISTAL system:

    .. image:: ./img/228-0.png
       :align: center
       :class: with-border
       

    .. note::

        The level field in the shorelines table corresponds to the level value in the GSHHS database: a value of 1 represents a coastline, 2 represents a lake, 3 represents an island within a lake, and 4 represents a pond on an island in a lake.

    While this is very simple, it's enough to get us started. Let's use this schema to create our database, firstly in MySQL:

    .. code-block:: python

        import MySQLdb

        connection = MySQLdb.connect(user="...", passwd="...")
        cursor = connection.cursor()

        cursor.execute("DROP DATABASE IF EXISTS distal")
        cursor.execute("CREATE DATABASE distal")
        cursor.execute("USE distal")
        cursor.execute("""
            CREATE TABLE countries (
                id       INTEGER AUTO_INCREMENT PRIMARY KEY,
                name     CHAR(255) CHARACTER SET utf8 NOT NULL,
                outline  GEOMETRY NOT NULL,
                
                SPATIAL INDEX (outline)) ENGINE=MyISAM
        """)

        cursor.execute("""
            CREATE TABLE shorelines (
                id      INTEGER AUTO_INCREMENT PRIMARY KEY,
                level   INTEGER NOT NULL,
                outline GEOMETRY NOT NULL,

                SPATIAL INDEX (outline)) ENGINE=MyISAM
        """)

        cursor.execute("""
            CREATE TABLE places (
                id       INTEGER AUTO_INCREMENT PRIMARY KEY,
                name     CHAR(255) CHARACTER SET utf8 NOT NULL,
                position POINT NOT NULL,

                SPATIAL INDEX (position)) ENGINE=MyISAM
        """)

        connection.commit()

    .. note:: 
        
        Note that we define the countries and places name fields to use UTF-8 character encoding. This allows us to store non-English names into these fields.

    The same code in PostGIS would look like this:

    .. code-block:: python

        import psycopg2

        connection = psycopg2.connect("dbname=... user=...")
        cursor = connection.cursor()

        cursor.execute("DROP TABLE IF EXISTS countries")
        cursor.execute("""
            CREATE TABLE countries (
                        id
                        SERIAL,
                        name VARCHAR(255),

                        PRIMARY KEY (id))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('countries', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            CREATE INDEX countryIndex ON countries
                USING GIST(outline)
        """)

        cursor.execute("DROP TABLE IF EXISTS shorelines")
        cursor.execute("""
            CREATE TABLE shorelines (
                id    SERIAL,
                level INTEGER,

                PRIMARY KEY (id))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('shorelines', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            CREATE INDEX shorelineIndex ON shorelines
                USING GIST(outline)
        """)

        cursor.execute("DROP TABLE IF EXISTS places")
        cursor.execute("""
            CREATE TABLE places (
                id   SERIAL,
                name VARCHAR(255),

                PRIMARY KEY (id))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('places', 'position',
                                      4326, 'POINT', 2)
        """)

        cursor.execute("""
            CREATE INDEX placeIndex ON places
                USING GIST(position)
        """)

        connection.commit()

    .. note::

        Note how the PostGIS version allows us to specify the SRID value for the geometry columns. We'll be using the WG84 datum and unprojected lat/long coordinates for all our spatial data, which is why we specified SRID 4326 when we created our geometries.

    And finally, using SpatiaLite:

    .. code-block:: python

        import os, os.path
        from pysqlite2 import dbapi2 as sqlite

        if os.path.exists("distal.db"):
            os.remove("distal.db")
        
        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()
        
        # Initialize the SpatiaLite meta-tables.
        
        cursor.execute('SELECT InitSpatialMetaData()')
        
        # Create the database tables.
        
        cursor.execute("DROP TABLE IF EXISTS countries")
        cursor.execute("""
            CREATE TABLE countries (
                id   INTEGER PRIMARY KEY AUTOINCREMENT,
                name CHAR(255))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('countries', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            SELECT CreateSpatialIndex('countries', 'outline')
        """)

        cursor.execute("DROP TABLE IF EXISTS shorelines")
        cursor.execute("""
            CREATE TABLE shorelines (
                id    INTEGER PRIMARY KEY AUTOINCREMENT,
                level INTEGER)
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('shorelines', 'outline',
                                      4326, 'GEOMETRY', 2)
        """)

        cursor.execute("""
            SELECT CreateSpatialIndex('shorelines', 'outline')
        """)

        cursor.execute("DROP TABLE IF EXISTS places")
        cursor.execute("""
            CREATE TABLE places (
                id   INTEGER PRIMARY KEY AUTOINCREMENT,
                name CHAR(255))
        """)

        cursor.execute("""
            SELECT AddGeometryColumn('places', 'position',
                                      4326, 'POINT', 2)
        """)

        cursor.execute("""
            SELECT CreateSpatialIndex('places', 'position')
        """)
        db.commit()
    
    Now that we've set up our database, let's get the data we need for the DISTAL application.
