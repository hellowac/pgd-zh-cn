设计和构建数据库
============================================

Designing and building the database

.. tab:: 中文

    让我们通过思考 DISTAL 应用程序所需的各种数据来开始其设计：

    - **所有国家的列表**：每个国家需要包含一个可以展示给用户的简单边界地图。
    - **全球的海岸线和湖泊边界**。
    - **全球所有主要城市和城镇的列表**：对于每个城市/城镇，我们需要包含该城市/城镇的名称以及一个表示该城市或城镇位置的点。

    幸运的是，这些数据是现成可用的：

    - 国家列表及其轮廓包含在 **世界边界数据集** 中。
    - 海岸线和湖泊边界（以及其他水陆边界，如湖中的岛屿）可以通过 **GSHHS海岸线数据库** 轻松获取。

      城市和城镇数据可以从两个地方获取：**GNIS数据库** （http://geonames.usgs.gov/domestic）提供美国的官方地名数据，而 **GEOnet名称服务器** （http://earth-info.nga.mil/gns/html）提供世界其他地区的类似数据。

    通过查看这些数据源，我们可以开始为DISTAL系统设计数据库架构：

    .. image:: ./img/228-0.png  
    :align: center  
    :class: with-border  

    .. note::

        shorelines 表中的 `level` 字段对应于 GSHHS 数据库中的 `level` 值：值为 1 表示海岸线，2 表示湖泊，3 表示湖中的岛屿，4 表示湖中岛屿上的池塘。

    虽然这很简单，但足以让我们开始使用它。让我们使用这个架构来创建数据库，首先是 MySQL：

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

        请注意，我们将 `countries` 和 `places` 表中的 `name` 字段定义为使用 UTF-8 字符编码。这使我们能够将非英语名称存储到这些字段中。

    在 PostGIS 中相同的代码如下：

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

        请注意，PostGIS 版本允许我们为几何列指定 SRID 值。我们将使用 WG84 基准和未投影的经纬度坐标处理所有空间数据，这就是为什么在创建几何数据时指定了 SRID 4326。

    最后，使用 SpatiaLite：

    .. code-block:: python

        import os, os.path
        from pysqlite2 import dbapi2 as sqlite

        if os.path.exists("distal.db"):
            os.remove("distal.db")
        
        db = sqlite.connect("distal.db")
        db.enable_load_extension(True)
        db.execute('SELECT load_extension("...")')
        cursor = db.cursor()
        
        # 初始化 SpatiaLite 元数据表。
        
        cursor.execute('SELECT InitSpatialMetaData()')
        
        # 创建数据库表。
        
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

    现在我们已经设置好了数据库，让我们获取 DISTAL 应用程序所需的数据。

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
