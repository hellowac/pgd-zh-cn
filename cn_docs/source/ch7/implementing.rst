实现 DISTAL 应用程序
============================================

Implementing the DISTAL application

.. tab:: 中文

    Now that we have the data, we can start to implement the DISTAL application itself. To keep things simple, we will use CGI scripts to implement the user interface.

    .. note::

        CGI scripts aren't the only way we could implement the DISTAL application. Other possible approaches include using web application frameworks such as TurboGears or Django, using AJAX to write your own dynamic web application, using CherryPy (http://cherrypy.org) or even using tools such as Pyjamas (http://pyjs. org) to compile Python code into JavaScript. All of these approaches, however, are more complicated than CGI, and we will be making use of CGI scripts in this chapter to keep the code as straightforward as possible.

    Let's take a look at how our CGI scripts will implement the DISTAL application's workflow:

    .. image:: ./img/247-0.png
       :scale: 50
       :class: with-border
       :align: center

    As you can see, there are three separate CGI scripts, selectCountry.py, selectArea.py, and showResults.py, each implementing a distinct part of the DISTAL application.

    .. tip:: What is a CGI Script?

        While the details of writing CGI scripts are beyond the scope of this
        book, the basic concept is to print the raw HTML output to stdout,
        and to process the incoming CGI parameters from the browser using
        the built-in cgi module.

        To run a Python program as a CGI script, you have to do two things:
        first, you have to add a "shebang" line to the start of the script, like this:
        
            #!/usr/bin/python
        
        For MS Windows, add the following line:
        
            #!C:\Python27\python.exe -U
        
        The exact path you use will depend on where you have Python
        installed on your computer.

        The second thing you need to do, at least on Unix-like systems, is make
        your script executable. For example:
        
            chmod +x selectCountry.py
        
        For more information, see one of the CGI tutorials commonly available
        on the Internet, for example: http://wiki.python.org/moin/
        CgiScripts.

    Let's start by creating a simple web server capable of running our CGI scripts. With Python this is easy; simply create the following program, which we will call webServer.py:

    .. code-block:: python

        import BaseHTTPServer
        import CGIHTTPServer

        address = ('', 8000)
        handler = CGIHTTPServer.CGIHTTPRequestHandler
        server = BaseHTTPServer.HTTPServer(address, handler)
        server.serve_forever()

    Next, create a subdirectory named cgi-bin within the same directory as your
    webServer.py program. This subdirectory will hold the various CGI scripts
    you create.

    Running webServer.py will set up a web server at http://127.0.0.1:8000,
    which will execute any CGI scripts you place into the cgi-bin subdirectory.
    So, for example, to access the selectCountry.py script, you would enter the
    following URL into your web browser:

    http://127.0.0.1:8000/cgi-bin/selectCountry.py

.. tab:: 英文

    Now that we have the data, we can start to implement the DISTAL application itself. To keep things simple, we will use CGI scripts to implement the user interface.

    .. note::

        CGI scripts aren't the only way we could implement the DISTAL application. Other possible approaches include using web application frameworks such as TurboGears or Django, using AJAX to write your own dynamic web application, using CherryPy (http://cherrypy.org) or even using tools such as Pyjamas (http://pyjs. org) to compile Python code into JavaScript. All of these approaches, however, are more complicated than CGI, and we will be making use of CGI scripts in this chapter to keep the code as straightforward as possible.

    Let's take a look at how our CGI scripts will implement the DISTAL application's workflow:

    .. image:: ./img/247-0.png
       :scale: 50
       :class: with-border
       :align: center

    As you can see, there are three separate CGI scripts, selectCountry.py, selectArea.py, and showResults.py, each implementing a distinct part of the DISTAL application.

    .. tip:: What is a CGI Script?

        While the details of writing CGI scripts are beyond the scope of this
        book, the basic concept is to print the raw HTML output to stdout,
        and to process the incoming CGI parameters from the browser using
        the built-in cgi module.

        To run a Python program as a CGI script, you have to do two things:
        first, you have to add a "shebang" line to the start of the script, like this:
        
            #!/usr/bin/python
        
        For MS Windows, add the following line:
        
            #!C:\Python27\python.exe -U
        
        The exact path you use will depend on where you have Python
        installed on your computer.

        The second thing you need to do, at least on Unix-like systems, is make
        your script executable. For example:
        
            chmod +x selectCountry.py
        
        For more information, see one of the CGI tutorials commonly available
        on the Internet, for example: http://wiki.python.org/moin/
        CgiScripts.

    Let's start by creating a simple web server capable of running our CGI scripts. With Python this is easy; simply create the following program, which we will call webServer.py:

    .. code-block:: python

        import BaseHTTPServer
        import CGIHTTPServer

        address = ('', 8000)
        handler = CGIHTTPServer.CGIHTTPRequestHandler
        server = BaseHTTPServer.HTTPServer(address, handler)
        server.serve_forever()

    Next, create a subdirectory named cgi-bin within the same directory as your
    webServer.py program. This subdirectory will hold the various CGI scripts
    you create.

    Running webServer.py will set up a web server at http://127.0.0.1:8000,
    which will execute any CGI scripts you place into the cgi-bin subdirectory.
    So, for example, to access the selectCountry.py script, you would enter the
    following URL into your web browser:

    http://127.0.0.1:8000/cgi-bin/selectCountry.py

共享“数据库”模块
---------------------------------
The shared "database" module

.. tab:: 中文

    To make things easier, we'll put all the database-specific code into a separate module,
    which we'll call database.py. Here is the basic structure for this module, along with
    the implementation of the database.open() function, which we'll use
    in our CGI scripts to open a connection to the database:

    .. code-block:: python

        # database.py

        import os.path
        import pyproj
        from shapely.geometry import Polygon
        import shapely.wkt

        ##############################################################
        # Edit these constants as necessary to match your setup.

        MYSQL_DBNAME   = "distal"
        MYSQL_USERNAME = "XXX"
        MYSQL_PASSWORD = "XXX"

        POSTGIS_DBNAME = "distal"
        POSTGIS_USERNAME = "XXX"
        POSTGIS_PASSWORD = "XXX"

        SPATIALITE_DB_PATH = os.path.join(os.path.dirname(__file__),
                                          "..", "distal.db")

        DB_TYPE = "XXX"

        #############################################################

        def open():
            global _connection, _cursor

            if DB_TYPE == "MySQL":
                import MySQLdb
                _connection = MySQLdb.connect(user=MYSQL_USERNAME,
                                              passwd=MYSQL_PASSWORD)
                _cursor = _connection.cursor()
                _cursor.execute("USE "+MYSQL_DBNAME)
            elif DB_TYPE == "PostGIS":
                import psycopg2

                params = []
                params.append("dbname=" + POSTGIS_DBNAME)
                if POSTGIS_USERNAME != None:
                    params.append("user=" + POSTGIS_USERNAME)
                if POSTGIS_PASSWORD != None:
                    params.append("password=" + POSTGIS_PASSWORD)
                _connection = psycopg2.connect(" ".join(params))
                _cursor = _connection.cursor()
            elif DB_TYPE == "SpatiaLite":
                from pysqlite2 import dbapi2 as sqlite
                _connection = sqlite.connect(SPATIALITE_DBNAME)
                _connection.enable_load_extension(True)
                _connection.execute('SELECT load_extension("...")')
                _cursor = _connection.cursor()
            else:
                raise RuntimeError("Unknown database type: " +
                                   db_type)
    
    Make sure you place this database.py module into the same directory as your
    CGI scripts.

    Don't forget to edit the constants at the top of the module to match your particular
    setup, entering the appropriate database names, usernames and passwords, and
    so on.

    .. note::

        The SPATIALITE_DB_PATH constant is set to the absolute path to our distal.db file. We use the Python's built-in __file__ global to avoid having to hardwire paths into our module.

    Note that we use private global variables (prefixed with an underscore character) to
    store the database connection and cursor. This lets us access these variables later on,
    as we add more functions to this module.

.. tab:: 英文

    To make things easier, we'll put all the database-specific code into a separate module,
    which we'll call database.py. Here is the basic structure for this module, along with
    the implementation of the database.open() function, which we'll use
    in our CGI scripts to open a connection to the database:

    .. code-block:: python

        # database.py

        import os.path
        import pyproj
        from shapely.geometry import Polygon
        import shapely.wkt

        ##############################################################
        # Edit these constants as necessary to match your setup.

        MYSQL_DBNAME   = "distal"
        MYSQL_USERNAME = "XXX"
        MYSQL_PASSWORD = "XXX"

        POSTGIS_DBNAME = "distal"
        POSTGIS_USERNAME = "XXX"
        POSTGIS_PASSWORD = "XXX"

        SPATIALITE_DB_PATH = os.path.join(os.path.dirname(__file__),
                                          "..", "distal.db")

        DB_TYPE = "XXX"

        #############################################################

        def open():
            global _connection, _cursor

            if DB_TYPE == "MySQL":
                import MySQLdb
                _connection = MySQLdb.connect(user=MYSQL_USERNAME,
                                              passwd=MYSQL_PASSWORD)
                _cursor = _connection.cursor()
                _cursor.execute("USE "+MYSQL_DBNAME)
            elif DB_TYPE == "PostGIS":
                import psycopg2

                params = []
                params.append("dbname=" + POSTGIS_DBNAME)
                if POSTGIS_USERNAME != None:
                    params.append("user=" + POSTGIS_USERNAME)
                if POSTGIS_PASSWORD != None:
                    params.append("password=" + POSTGIS_PASSWORD)
                _connection = psycopg2.connect(" ".join(params))
                _cursor = _connection.cursor()
            elif DB_TYPE == "SpatiaLite":
                from pysqlite2 import dbapi2 as sqlite
                _connection = sqlite.connect(SPATIALITE_DBNAME)
                _connection.enable_load_extension(True)
                _connection.execute('SELECT load_extension("...")')
                _cursor = _connection.cursor()
            else:
                raise RuntimeError("Unknown database type: " +
                                   db_type)
    
    Make sure you place this database.py module into the same directory as your
    CGI scripts.

    Don't forget to edit the constants at the top of the module to match your particular
    setup, entering the appropriate database names, usernames and passwords, and
    so on.

    .. note::

        The SPATIALITE_DB_PATH constant is set to the absolute path to our distal.db file. We use the Python's built-in __file__ global to avoid having to hardwire paths into our module.

    Note that we use private global variables (prefixed with an underscore character) to
    store the database connection and cursor. This lets us access these variables later on,
    as we add more functions to this module.


“选择国家”脚本
---------------------------------
The "select country" script

.. tab:: 中文

    The task of the selectCountry.py script is to display a list of countries to the user, so that the user can choose a desired country which is then passed to the selectArea.py script for further processing.

    Here is what the selectCountry.py script's output will look like:

    .. image:: ./img/251-0.png
       :scale: 50
       :class: with-border
       :align: center

    This CGI script is very basic: we simply print out the contents of the HTML page which lets the user choose a country from a list of country names:

    .. code-block:: python

        #!/usr/bin/python

        import database
        database.open()

        print 'Content-Type: text/html; charset=UTF-8\n\n'
        print '<html>'
        print '<head><title>Select Country</title></head>'
        print '<body>'
        print '<form method="POST" action="selectArea.py">'
        print '<select name="countryID" size="10">'

        for id,name in database.list_countries():
            print '<option value="'+str(id)+'">'+name+'</option>'
        
        print '</select>'
        print '<p>'
        print '<input type="submit" value="OK">'
        print '</form>'
        print '</body>'
        print '</html>'

    .. tip:: Understanding HTML Forms

        If you haven't used HTML forms before, don't panic. They are quite straightforward, and if you want you can just copy the code from the examples given here. To learn more about HTML forms, check out one of the many tutorials available online. A good example can be found at http://www.pagetutor.com/form_tutor.

    As you can see, we call the list_countries() function within the database module
    to return a list of country record IDs and their associated names. The implementation
    of this function is straightforward; simply add the following code to your database.
    py module:

    .. code-block:: python

        def list_countries():
            global _cursor
            results = []
            _cursor.execute("SELECT id,name FROM countries " +
                            "ORDER BY name")
            for id,name in _cursor:
                results.append((id, name))
            return results

    Unfortunately, there is a problem with this code: because SpatiaLite can't handle
    UTF-8 character encoding, we have to manually convert the country name from
    Unicode to UTF-8 before returning it. We can do this by adding the following
    highlighted lines to our function:

    .. code-block:: python
        ...
        for id,name in _cursor:
            if DB_TYPE == "SpatiaLite":
                name = name.encode("utf-8")
                results.append((id, name))
        ...
    
    This completes the "select country" script. You should now be able to run it by typing
    the following URL in your web browser:

    http://127.0.0.1:8000/cgi-bin/selectCountry.py
    
    All going well, you should see the list of countries and be able to select one. If you
    click on the **OK** button, you should see a 404 error, indicating that the selectArea.
    py script doesn't exist yet—which is perfectly correct, as we haven't implemented
    it yet.

.. tab:: 英文

    The task of the selectCountry.py script is to display a list of countries to the user, so that the user can choose a desired country which is then passed to the selectArea.py script for further processing.

    Here is what the selectCountry.py script's output will look like:

    .. image:: ./img/251-0.png
       :scale: 50
       :class: with-border
       :align: center

    This CGI script is very basic: we simply print out the contents of the HTML page which lets the user choose a country from a list of country names:

    .. code-block:: python

        #!/usr/bin/python

        import database
        database.open()

        print 'Content-Type: text/html; charset=UTF-8\n\n'
        print '<html>'
        print '<head><title>Select Country</title></head>'
        print '<body>'
        print '<form method="POST" action="selectArea.py">'
        print '<select name="countryID" size="10">'

        for id,name in database.list_countries():
            print '<option value="'+str(id)+'">'+name+'</option>'
        
        print '</select>'
        print '<p>'
        print '<input type="submit" value="OK">'
        print '</form>'
        print '</body>'
        print '</html>'

    .. tip:: Understanding HTML Forms

        If you haven't used HTML forms before, don't panic. They are quite straightforward, and if you want you can just copy the code from the examples given here. To learn more about HTML forms, check out one of the many tutorials available online. A good example can be found at http://www.pagetutor.com/form_tutor.

    As you can see, we call the list_countries() function within the database module
    to return a list of country record IDs and their associated names. The implementation
    of this function is straightforward; simply add the following code to your database.
    py module:

    .. code-block:: python

        def list_countries():
            global _cursor
            results = []
            _cursor.execute("SELECT id,name FROM countries " +
                            "ORDER BY name")
            for id,name in _cursor:
                results.append((id, name))
            return results

    Unfortunately, there is a problem with this code: because SpatiaLite can't handle
    UTF-8 character encoding, we have to manually convert the country name from
    Unicode to UTF-8 before returning it. We can do this by adding the following
    highlighted lines to our function:

    .. code-block:: python
        ...
        for id,name in _cursor:
            if DB_TYPE == "SpatiaLite":
                name = name.encode("utf-8")
                results.append((id, name))
        ...
    
    This completes the "select country" script. You should now be able to run it by typing
    the following URL in your web browser:

    http://127.0.0.1:8000/cgi-bin/selectCountry.py
    
    All going well, you should see the list of countries and be able to select one. If you
    click on the **OK** button, you should see a 404 error, indicating that the selectArea.
    py script doesn't exist yet—which is perfectly correct, as we haven't implemented
    it yet.


“选择区域”脚本
---------------------------------
The "select area" script

.. tab:: 中文

    The next part of the DISTAL application is selectArea.py. This script generates a web page that displays a simple map of the selected country. The user can enter a desired search radius and click on the map to identify the starting point for the DISTAL search:

    .. image:: ./img/253-0.png
       :class: with-border
       :align: center

    For this script to work, we're going to need some way of generating a map. Map
    generation using the Mapnik toolkit will be covered in detail in Chapter 8, Using
    Python and Mapnik to Generate Maps; for now, we are going to create a standalone
    mapGenerator.py module, which does the map rendering for us so that we can
    focus on the other aspects of the DISTAL application.
    Here is the full source code for the mapGenerator.py module, which should be
    placed in your cgi-bin directory:

    .. code-block:: python

        # mapGenerator.py

        import os, os.path, sys, tempfile
        import mapnik

        def generateMap(datasource, minX, minY, maxX, maxY,
                        mapWidth, mapHeight,
                        hiliteExpr=None, background="#8080a0",
                        hiliteLine="#000000", hiliteFill="#408000",
                        normalLine="#404040", normalFill="#a0a0a0",
                        points=None):
        srcType = datasource['type']
        del datasource['type']

        if srcType == "OGR":
            source = mapnik.Ogr(**datasource)
        elif srcType == "PostGIS":
            source = mapnik.PostGIS(**datasource)
        elif srcType == "SQLite":
            source = mapnik.SQLite(**datasource)
        
        layer = mapnik.Layer("Layer")
        layer.datasource = source

        map = mapnik.Map(mapWidth, mapHeight,
                         '+proj=longlat +datum=WGS84')
        map.background = mapnik.Color(background)

        style = mapnik.Style()

        rule = mapnik.Rule()
        
        if hiliteExpr != None:
            rule.filter = mapnik.Filter(hiliteExpr)
        
        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(hiliteFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(hiliteLine), 0.1)))
        
        style.rules.append(rule)
        
        rule = mapnik.Rule()
        rule.set_else(True)
        
        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(normalFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(normalLine), 0.1)))
        
        style.rules.append(rule)
        
        map.append_style("Map Style", style)
        layer.styles.append("Map Style")
        map.layers.append(layer)
        
        if points != None:
            memoryDatasource = mapnik.MemoryDatasource()
            context = mapnik.Context()
            context.push("name")
            next_id = 1
            for long,lat,name in points:
                wkt = "POINT (%0.8f %0.8f)" % (long,lat)
                feature = mapnik.Feature(context, next_id)
                feature['name'] = name
                feature.add_geometries_from_wkt(wkt)
                next_id = next_id + 1
                memoryDatasource.add_feature(feature)

            layer = mapnik.Layer("Points")
            layer.datasource = memoryDatasource

            style = mapnik.Style()
            rule = mapnik.Rule()

            pointImgFile = os.path.join(os.path.dirname(__file__),
                                        "point.png")

            shield = mapnik.ShieldSymbolizer(
                        mapnik.Expression('[name]'),
                        "DejaVu Sans Bold", 10,
                        mapnik.Color("#000000"),
                        mapnik.PathExpression(pointImgFile))
            shield.displacement(0, 7)
            shield.unlock_image = True
            rule.symbols.append(shield)

            style.rules.append(rule)

            map.append_style("Point Style", style)
            layer.styles.append("Point Style")
            
            map.layers.append(layer)
        
        map.zoom_to_box(mapnik.Envelope(minX, minY, maxX, maxY))
        
        scriptDir = os.path.dirname(__file__)
        cacheDir = os.path.join(scriptDir, "..", "mapCache")
        if not os.path.exists(cacheDir):
            os.mkdir(cacheDir)
        fd,filename = tempfile.mkstemp(".png", dir=cacheDir)
        os.close(fd)

        mapnik.render_to_file(map, filename, "png")
        
        return "../mapCache/" + os.path.basename(filename)

    Don't worry too much about the details of this module; everything will be explained in Chapter 8, Using Python and Mapnik to Generate Maps. In the meantime, just use this module as written. There are just two things to be aware of:

    - You need to have Mapnik installed on your computer for this module to work. The Mapnik toolkit can be found at http://mapnik.org.
    - This module requires a small image file that is used to mark place names on the map. This 9 x 9 pixel image looks like this:
    
      .. image:: ./img/256-0.png
         :align: center
         

      This preceding image is available as part of the example source code that comes with this book. If you don't have access to the example code, you can create or search for an image that looks like this; make sure the image is named point.png and is placed into the same directory as the mapGenerator.py module itself.

    We're now ready to start looking at the selectArea.py script itself. We'll start with our shebang line and import the various modules we'll need:

    .. code-block:: python

        #!/usr/bin/python

        import cgi, os.path, sys
        import shapely.wkt
        import database
        import mapGenerator

    Next, we define some useful constants:

    .. code-block:: python

        HEADER = "Content-Type: text/html; charset=UTF-8\n\n" \
               + "<html><head><title>Select Area</title>" \
               + "</head><body>"
        
        FOOTER = "</body></html>"

        MAX_WIDTH = 600
        MAX_HEIGHT = 400

    Then we open up the database:

    .. code-block:: python
    
        database.open()

    Our next task is to extract the ID of the country the user clicked on:

    .. code-block:: python
            
        form = cgi.FieldStorage()
        if not form.has_key("countryID"):
            print HEADER
            print '<b>Please select a country</b>'
            print FOOTER
            sys.exit(0)

        countryID = int(form['countryID'].value)

    Now that we have the ID of the selected country, we're ready to start generating the
    map. Doing this is a four-step process:
    
    - Calculate the bounding box that defines the portion of the world to be displayed
    - Calculate the map's dimensions
    - Set up the data source
    - Render the map image
    
    Let's look at each of these in turn.

.. tab:: 英文

    The next part of the DISTAL application is selectArea.py. This script generates a web page that displays a simple map of the selected country. The user can enter a desired search radius and click on the map to identify the starting point for the DISTAL search:

    .. image:: ./img/253-0.png
       :class: with-border
       :align: center

    For this script to work, we're going to need some way of generating a map. Map
    generation using the Mapnik toolkit will be covered in detail in Chapter 8, Using
    Python and Mapnik to Generate Maps; for now, we are going to create a standalone
    mapGenerator.py module, which does the map rendering for us so that we can
    focus on the other aspects of the DISTAL application.
    Here is the full source code for the mapGenerator.py module, which should be
    placed in your cgi-bin directory:

    .. code-block:: python

        # mapGenerator.py

        import os, os.path, sys, tempfile
        import mapnik

        def generateMap(datasource, minX, minY, maxX, maxY,
                        mapWidth, mapHeight,
                        hiliteExpr=None, background="#8080a0",
                        hiliteLine="#000000", hiliteFill="#408000",
                        normalLine="#404040", normalFill="#a0a0a0",
                        points=None):
        srcType = datasource['type']
        del datasource['type']

        if srcType == "OGR":
            source = mapnik.Ogr(**datasource)
        elif srcType == "PostGIS":
            source = mapnik.PostGIS(**datasource)
        elif srcType == "SQLite":
            source = mapnik.SQLite(**datasource)
        
        layer = mapnik.Layer("Layer")
        layer.datasource = source

        map = mapnik.Map(mapWidth, mapHeight,
                         '+proj=longlat +datum=WGS84')
        map.background = mapnik.Color(background)

        style = mapnik.Style()

        rule = mapnik.Rule()
        
        if hiliteExpr != None:
            rule.filter = mapnik.Filter(hiliteExpr)
        
        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(hiliteFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(hiliteLine), 0.1)))
        
        style.rules.append(rule)
        
        rule = mapnik.Rule()
        rule.set_else(True)
        
        rule.symbols.append(mapnik.PolygonSymbolizer(
            mapnik.Color(normalFill)))
        rule.symbols.append(mapnik.LineSymbolizer(
            mapnik.Stroke(mapnik.Color(normalLine), 0.1)))
        
        style.rules.append(rule)
        
        map.append_style("Map Style", style)
        layer.styles.append("Map Style")
        map.layers.append(layer)
        
        if points != None:
            memoryDatasource = mapnik.MemoryDatasource()
            context = mapnik.Context()
            context.push("name")
            next_id = 1
            for long,lat,name in points:
                wkt = "POINT (%0.8f %0.8f)" % (long,lat)
                feature = mapnik.Feature(context, next_id)
                feature['name'] = name
                feature.add_geometries_from_wkt(wkt)
                next_id = next_id + 1
                memoryDatasource.add_feature(feature)

            layer = mapnik.Layer("Points")
            layer.datasource = memoryDatasource

            style = mapnik.Style()
            rule = mapnik.Rule()

            pointImgFile = os.path.join(os.path.dirname(__file__),
                                        "point.png")

            shield = mapnik.ShieldSymbolizer(
                        mapnik.Expression('[name]'),
                        "DejaVu Sans Bold", 10,
                        mapnik.Color("#000000"),
                        mapnik.PathExpression(pointImgFile))
            shield.displacement(0, 7)
            shield.unlock_image = True
            rule.symbols.append(shield)

            style.rules.append(rule)

            map.append_style("Point Style", style)
            layer.styles.append("Point Style")
            
            map.layers.append(layer)
        
        map.zoom_to_box(mapnik.Envelope(minX, minY, maxX, maxY))
        
        scriptDir = os.path.dirname(__file__)
        cacheDir = os.path.join(scriptDir, "..", "mapCache")
        if not os.path.exists(cacheDir):
            os.mkdir(cacheDir)
        fd,filename = tempfile.mkstemp(".png", dir=cacheDir)
        os.close(fd)

        mapnik.render_to_file(map, filename, "png")
        
        return "../mapCache/" + os.path.basename(filename)

    Don't worry too much about the details of this module; everything will be explained in Chapter 8, Using Python and Mapnik to Generate Maps. In the meantime, just use this module as written. There are just two things to be aware of:

    - You need to have Mapnik installed on your computer for this module to work. The Mapnik toolkit can be found at http://mapnik.org.
    - This module requires a small image file that is used to mark place names on the map. This 9 x 9 pixel image looks like this:
    
      .. image:: ./img/256-0.png
         :align: center
         

      This preceding image is available as part of the example source code that comes with this book. If you don't have access to the example code, you can create or search for an image that looks like this; make sure the image is named point.png and is placed into the same directory as the mapGenerator.py module itself.

    We're now ready to start looking at the selectArea.py script itself. We'll start with our shebang line and import the various modules we'll need:

    .. code-block:: python

        #!/usr/bin/python

        import cgi, os.path, sys
        import shapely.wkt
        import database
        import mapGenerator

    Next, we define some useful constants:

    .. code-block:: python

        HEADER = "Content-Type: text/html; charset=UTF-8\n\n" \
               + "<html><head><title>Select Area</title>" \
               + "</head><body>"
        
        FOOTER = "</body></html>"

        MAX_WIDTH = 600
        MAX_HEIGHT = 400

    Then we open up the database:

    .. code-block:: python
    
        database.open()

    Our next task is to extract the ID of the country the user clicked on:

    .. code-block:: python
            
        form = cgi.FieldStorage()
        if not form.has_key("countryID"):
            print HEADER
            print '<b>Please select a country</b>'
            print FOOTER
            sys.exit(0)

        countryID = int(form['countryID'].value)

    Now that we have the ID of the selected country, we're ready to start generating the
    map. Doing this is a four-step process:
    
    - Calculate the bounding box that defines the portion of the world to be displayed
    - Calculate the map's dimensions
    - Set up the data source
    - Render the map image
    
    Let's look at each of these in turn.


计算边界框
~~~~~~~~~~~~
Calculating the bounding box

.. tab:: 中文

    Before we can show the selected country on a map, we need to calculate the bounding
    box for that country—that is, the minimum and maximum latitude and longitude
    values. Knowing the bounding box allows us to draw a map centered over the desired
    country. If we didn't do this, the map would cover the entire world.

    Let's start by adding a function to our database module to extract the information
    we need about the selected country:

    .. code-block:: python

        def get_country_details(country_id):
            global _cursor

            if DB_TYPE == "MySQL":
                _cursor.execute("SELECT name," +
                                "AsText(Envelope(outline)) " +
                                "FROM countries WHERE id=%s",
                                (country_id,))
            elif DB_TYPE == "PostGIS":
                _cursor.execute("SELECT name," +
                                "ST_AsText(ST_Envelope(outline)) " +
                                "FROM countries WHERE id=%s",
                                (country_id,))
            elif DB_TYPE == "SpatiaLite":
                _cursor.execute("SELECT name," +
                                "ST_AsText(ST_Envelope(outline)) " +
                                "FROM countries WHERE id=?",
                                (country_id,))

            row = _cursor.fetchone()
            if row != None:
                return {'name'       : row[0],
                        'bounds_wkt' : row[1]}
            else:
                return None

    This function returns the given country's name and its bounding box as a
    WKT-format string. Note how we first calculate the envelope (or bounding box)
    for the country's outline, and then convert that envelope into a WKT string using
    the AsText function.

    With this function in place, we can now add the necessary code to our selectArea.
    py script to calculate the area of the world to display on our map; simply add the
    following to the end of your CGI script:

    .. code-block:: python

        details = database.get_country_details(countryID)

        envelope = shapely.wkt.loads(details['bounds_wkt'])
        minLong,minLat,maxLong,maxLat = envelope.bounds
        minLong = minLong - 0.2
        minLat = minLat - 0.2
        maxLong = maxLong + 0.2
        maxLat = maxLat + 0.2

    As you can see, we use Shapely to extract the minimum and maximum latitude and
    longitude values, and then increase these bounds slightly so that the country won't
    butt up against the edge of the map.

    There's just one problem with our code: if an invalid country ID was specified, our
    program will crash. To get around this, add the following error-handling code to
    the script, immediately below the call to database.get_country_details():

    .. code-block:: python

        if details == None:
            print HEADER
            print '<b>Missing Country ' + repr(countryID) + '</b>'
            print FOOTER
            sys.exit(0)

.. tab:: 英文

    Before we can show the selected country on a map, we need to calculate the bounding
    box for that country—that is, the minimum and maximum latitude and longitude
    values. Knowing the bounding box allows us to draw a map centered over the desired
    country. If we didn't do this, the map would cover the entire world.

    Let's start by adding a function to our database module to extract the information
    we need about the selected country:

    .. code-block:: python

        def get_country_details(country_id):
            global _cursor

            if DB_TYPE == "MySQL":
                _cursor.execute("SELECT name," +
                                "AsText(Envelope(outline)) " +
                                "FROM countries WHERE id=%s",
                                (country_id,))
            elif DB_TYPE == "PostGIS":
                _cursor.execute("SELECT name," +
                                "ST_AsText(ST_Envelope(outline)) " +
                                "FROM countries WHERE id=%s",
                                (country_id,))
            elif DB_TYPE == "SpatiaLite":
                _cursor.execute("SELECT name," +
                                "ST_AsText(ST_Envelope(outline)) " +
                                "FROM countries WHERE id=?",
                                (country_id,))

            row = _cursor.fetchone()
            if row != None:
                return {'name'       : row[0],
                        'bounds_wkt' : row[1]}
            else:
                return None

    This function returns the given country's name and its bounding box as a
    WKT-format string. Note how we first calculate the envelope (or bounding box)
    for the country's outline, and then convert that envelope into a WKT string using
    the AsText function.

    With this function in place, we can now add the necessary code to our selectArea.
    py script to calculate the area of the world to display on our map; simply add the
    following to the end of your CGI script:

    .. code-block:: python

        details = database.get_country_details(countryID)

        envelope = shapely.wkt.loads(details['bounds_wkt'])
        minLong,minLat,maxLong,maxLat = envelope.bounds
        minLong = minLong - 0.2
        minLat = minLat - 0.2
        maxLong = maxLong + 0.2
        maxLat = maxLat + 0.2

    As you can see, we use Shapely to extract the minimum and maximum latitude and
    longitude values, and then increase these bounds slightly so that the country won't
    butt up against the edge of the map.

    There's just one problem with our code: if an invalid country ID was specified, our
    program will crash. To get around this, add the following error-handling code to
    the script, immediately below the call to database.get_country_details():

    .. code-block:: python
        
        if details == None:
            print HEADER
            print '<b>Missing Country ' + repr(countryID) + '</b>'
            print FOOTER
            sys.exit(0)


计算地图尺寸
~~~~~~~~~~~~
Calculating the map's dimensions

.. tab:: 中文

    The bounding box isn't useful only to zoom in on the desired part of the map: it also
    helps us to correctly define the map's dimensions. Note that the preceding map of
    Albania shows the country as being taller than it is wide. If you were to naively
    draw this map as a square image, Albania would end up looking like this:

    .. image:: ./img/260-0.png
       :class: with-border
       :align: center

    Even worse, Chile would look like this:

    .. image:: ./img/260-1.png
       :class: with-border
       :align: center

    Rather than this:

    .. image:: ./img/261-0.png
       :class: with-border
       :align: center

    .. note::
        
        This is a slight simplification; the mapping toolkits generally do try to preserve the aspect ratio for a map, but their behavior is unpredictable and means that you can't identify the lat/long coordinates for a clicked-on point.

    To display the country correctly, we need to calculate the country's aspect ratio
    (its width as a proportion of its height) and then calculate the size of the map image
    based on this aspect ratio, while limiting the overall size of the image so that it can
    fit within a web page. Here's the necessary code, which you should add to the end
    of your selectArea.py script:

    .. code-block:: python

        width = float(maxLong - minLong)
        height = float(maxLat - minLat)
        aspectRatio = width/height

        mapWidth = MAX_WIDTH
        mapHeight = int(mapWidth / aspectRatio)
        
        if mapHeight > MAX_HEIGHT:
            # Scale the map to fit.
            scaleFactor = float(MAX_HEIGHT) / float(mapHeight)
            mapWidth = int(mapWidth * scaleFactor)
            mapHeight = int(mapHeight * scaleFactor)

    Doing this means that the map is correctly sized to reflect the dimensions of the country we are displaying.

.. tab:: 英文

    The bounding box isn't useful only to zoom in on the desired part of the map: it also
    helps us to correctly define the map's dimensions. Note that the preceding map of
    Albania shows the country as being taller than it is wide. If you were to naively
    draw this map as a square image, Albania would end up looking like this:

    .. image:: ./img/260-0.png
       :class: with-border
       :align: center

    Even worse, Chile would look like this:

    .. image:: ./img/260-1.png
       :class: with-border
       :align: center

    Rather than this:

    .. image:: ./img/261-0.png
       :class: with-border
       :align: center

    .. note::
        
        This is a slight simplification; the mapping toolkits generally do try to preserve the aspect ratio for a map, but their behavior is unpredictable and means that you can't identify the lat/long coordinates for a clicked-on point.

    To display the country correctly, we need to calculate the country's aspect ratio
    (its width as a proportion of its height) and then calculate the size of the map image
    based on this aspect ratio, while limiting the overall size of the image so that it can
    fit within a web page. Here's the necessary code, which you should add to the end
    of your selectArea.py script:

    .. code-block:: python

        width = float(maxLong - minLong)
        height = float(maxLat - minLat)
        aspectRatio = width/height

        mapWidth = MAX_WIDTH
        mapHeight = int(mapWidth / aspectRatio)
        
        if mapHeight > MAX_HEIGHT:
            # Scale the map to fit.
            scaleFactor = float(MAX_HEIGHT) / float(mapHeight)
            mapWidth = int(mapWidth * scaleFactor)
            mapHeight = int(mapHeight * scaleFactor)

    Doing this means that the map is correctly sized to reflect the dimensions of the country we are displaying.


设置数据源
~~~~~~~~~~~~
Setting up the data source

.. tab:: 中文

    The data source tells the map generator how to access the underlying map data.
    How data sources work is beyond the scope of this chapter; for now, we are simply
    going to set up the required datasource dictionary and related files so that we can
    generate our map. Note that the contents of this dictionary will vary depending on
    which database you are using, as well as which table you are trying to access; in this
    case, we are trying to display selected features from the countries table. To handle
    this, we'll create a new function within our database module to set up the data
    source for our particular database:

    .. code-block:: python

        def get_country_datasource():
            if DB_TYPE == "MySQL":
                vrtFile = os.path.join(os.path.dirname(__file__),
                                    "countries.vrt")

                f = file(vrtFile, "w")
                f.write('<OGRVRTDataSource>\n')
                f.write(' <OGRVRTLayer name="countries">\n')
                f.write('   <SrcDataSource>MySQL:' + MYSQL_DBNAME)
                if MYSQL_USERNAME != None:
                    f.write(",user=" + MYSQL_USERNAME)
                if MYSQL_PASSWORD != None:
                    f.write(",passwd=" + MYSQL_PASSWORD)
                f.write('</SrcDataSource>\n')
                f.write('   <SrcSQL>SELECT id,outline ' +
                            'FROM countries</SrcSQL>\n')
                f.write(' </OGRVRTLayer>\n')
                f.write('</OGRVRTDataSource>\n')
                f.close()

                return {'type' : "OGR",
                        'file' : vrtFile,
                        'layer' : "countries"}

            elif DB_TYPE == "PostGIS":
                return {'type': "PostGIS",
                        'dbname': "distal",
                        'table': "countries",
                        'user': POSTGIS_USERNAME,
                        'password' : POSTGIS_PASSWORD}
            elif DB_TYPE == "SpatiaLite":
                return {'type': "SQLite",
                        'file': SPATIALITE_DBNAME,
                        'table': "countries",
                        'geometry_field' : "outline",
                        'key_field': "id"}

    MySQL uses what is called a "virtual datasource", which is a special file that
    tells Mapnik how to access the data. We create this file as we need it, storing
    the username and other details into the file as required.

    .. note:: 
        
        Note that we are storing the countries.vrt file in the same directory as our CGI scripts. This makes it easier to access this file from Mapnik.
    
    Now that we have written the get_datasource() function, it's time to use it.
    Add the following line to the end of your selectArea.py script:

    datasource = database.get_country_datasource()

.. tab:: 英文

    The data source tells the map generator how to access the underlying map data.
    How data sources work is beyond the scope of this chapter; for now, we are simply
    going to set up the required datasource dictionary and related files so that we can
    generate our map. Note that the contents of this dictionary will vary depending on
    which database you are using, as well as which table you are trying to access; in this
    case, we are trying to display selected features from the countries table. To handle
    this, we'll create a new function within our database module to set up the data
    source for our particular database:

    .. code-block:: python

        def get_country_datasource():
            if DB_TYPE == "MySQL":
                vrtFile = os.path.join(os.path.dirname(__file__),
                                    "countries.vrt")

                f = file(vrtFile, "w")
                f.write('<OGRVRTDataSource>\n')
                f.write(' <OGRVRTLayer name="countries">\n')
                f.write('   <SrcDataSource>MySQL:' + MYSQL_DBNAME)
                if MYSQL_USERNAME != None:
                    f.write(",user=" + MYSQL_USERNAME)
                if MYSQL_PASSWORD != None:
                    f.write(",passwd=" + MYSQL_PASSWORD)
                f.write('</SrcDataSource>\n')
                f.write('   <SrcSQL>SELECT id,outline ' +
                            'FROM countries</SrcSQL>\n')
                f.write(' </OGRVRTLayer>\n')
                f.write('</OGRVRTDataSource>\n')
                f.close()

                return {'type' : "OGR",
                        'file' : vrtFile,
                        'layer' : "countries"}

            elif DB_TYPE == "PostGIS":
                return {'type': "PostGIS",
                        'dbname': "distal",
                        'table': "countries",
                        'user': POSTGIS_USERNAME,
                        'password' : POSTGIS_PASSWORD}
            elif DB_TYPE == "SpatiaLite":
                return {'type': "SQLite",
                        'file': SPATIALITE_DBNAME,
                        'table': "countries",
                        'geometry_field' : "outline",
                        'key_field': "id"}

    MySQL uses what is called a "virtual datasource", which is a special file that
    tells Mapnik how to access the data. We create this file as we need it, storing
    the username and other details into the file as required.

    .. note:: 
        
        Note that we are storing the countries.vrt file in the same directory as our CGI scripts. This makes it easier to access this file from Mapnik.
    
    Now that we have written the get_datasource() function, it's time to use it.
    Add the following line to the end of your selectArea.py script:

    datasource = database.get_country_datasource()


渲染地图图像
~~~~~~~~~~~~
Rendering the map image

.. tab:: 中文

    With the bounding box, the map's dimensions and the data source all set up, we
    are finally ready to render the map into an image file. This is done using a single
    function call as follows:

    .. code-block:: python

        imgFile = mapGenerator.generateMap(datasource,
                                           minLong, minLat,
                                           maxLong, maxLat,
                                           mapWidth, mapHeight,
                                           "[id] = "+str(countryID))

    Note that our datasource has been set up to display features from the countries
    table, and that the "[id] = "+str(countryID) is a "highlight expression" is used
    to visually highlight the country with the given ID.

    The mapGenerator.generateMap() function returns a reference to a PNG-format
    image file containing the generated map. This image file is stored in a temporary
    directory, and the file's relative pathname is returned to the caller. This allows us
    to use the returned imgFile directly within our CGI script, like this:

    .. code-block:: python

        print 'Content-Type: text/html; charset=UTF-8\n\n'
        print '<html>'
        print '<head><title>Select Area</title></head>'
        print '<body>'
        print '<b>' + name + '</b>'
        print '<p>'
        print '<form method="POST" action="showResults.py">'
        print 'Select all features within'
        print '<input type="text" name="radius" value="10" size="2">'
        print 'miles of a point.'
        print '<p>'
        print 'Click on the map to identify your starting point:'
        print '<br>'
        print '<input type="image" src="' + imgFile + '" ismap>'
        print '<input type="hidden" name="countryID"'
        print '
        value="' + str(countryID) + '">'
        print '<input type="hidden" name="mapWidth"'
        print '
        value="' + str(mapWidth) + '">'
        print '<input type="hidden" name="mapHeight"'
        print '
        value="' + str(mapHeight) + '">'
        print '</form>'
        print '</body></html>'

    .. note::

        The <input type="hidden"> lines define "hidden form fields" that pass information on to the next CGI script. We'll discuss how this information is used in the next section.

    The use of *<input type="image" src="..." ismap>* in this CGI script has the
    interesting effect of making the map clickable: when the user clicks on the image, the
    enclosing HTML form will be submitted with two extra parameters named x and y.
    These contain the coordinate within the image that the user clicked on.

    This completes the *selectArea.py* CGI script. Make sure you added an appropriate
    "shebang" line to the start of your program and made it executable, as described
    earlier, so that it can run as a CGI script.

    All going well, you should be able to point your web browser to:

    http://127.0.0.1:8000/cgi-bin/selectCountry.py

    Choose a country, and see a map of that country displayed within your web browser.
    If you click within the map, you'll get a 404 error, indicating that the final CGI script
    hasn't been written yet.

.. tab:: 英文

    With the bounding box, the map's dimensions and the data source all set up, we
    are finally ready to render the map into an image file. This is done using a single
    function call as follows:

    .. code-block:: python

        imgFile = mapGenerator.generateMap(datasource,
                                           minLong, minLat,
                                           maxLong, maxLat,
                                           mapWidth, mapHeight,
                                           "[id] = "+str(countryID))

    Note that our datasource has been set up to display features from the countries
    table, and that the "[id] = "+str(countryID) is a "highlight expression" is used
    to visually highlight the country with the given ID.

    The mapGenerator.generateMap() function returns a reference to a PNG-format
    image file containing the generated map. This image file is stored in a temporary
    directory, and the file's relative pathname is returned to the caller. This allows us
    to use the returned imgFile directly within our CGI script, like this:

    .. code-block:: python

        print 'Content-Type: text/html; charset=UTF-8\n\n'
        print '<html>'
        print '<head><title>Select Area</title></head>'
        print '<body>'
        print '<b>' + name + '</b>'
        print '<p>'
        print '<form method="POST" action="showResults.py">'
        print 'Select all features within'
        print '<input type="text" name="radius" value="10" size="2">'
        print 'miles of a point.'
        print '<p>'
        print 'Click on the map to identify your starting point:'
        print '<br>'
        print '<input type="image" src="' + imgFile + '" ismap>'
        print '<input type="hidden" name="countryID"'
        print '
        value="' + str(countryID) + '">'
        print '<input type="hidden" name="mapWidth"'
        print '
        value="' + str(mapWidth) + '">'
        print '<input type="hidden" name="mapHeight"'
        print '
        value="' + str(mapHeight) + '">'
        print '</form>'
        print '</body></html>'

    .. note::

        The <input type="hidden"> lines define "hidden form fields" that pass information on to the next CGI script. We'll discuss how this information is used in the next section.

    The use of *<input type="image" src="..." ismap>* in this CGI script has the
    interesting effect of making the map clickable: when the user clicks on the image, the
    enclosing HTML form will be submitted with two extra parameters named x and y.
    These contain the coordinate within the image that the user clicked on.

    This completes the *selectArea.py* CGI script. Make sure you added an appropriate
    "shebang" line to the start of your program and made it executable, as described
    earlier, so that it can run as a CGI script.

    All going well, you should be able to point your web browser to:

    http://127.0.0.1:8000/cgi-bin/selectCountry.py

    Choose a country, and see a map of that country displayed within your web browser.
    If you click within the map, you'll get a 404 error, indicating that the final CGI script
    hasn't been written yet.


“显示结果”脚本
---------------------------------
The "show results" script

.. tab:: 中文

    The final CGI script is where the real work is done. Start by creating your
    showResults.py file, and type the following into this file:

    .. code-block:: python

        #!/usr/bin/env python

        import cgi
        import pyproj

        import database
        import mapGenerator

        ############################################################

        MAX_WIDTH = 1000
        MAX_HEIGHT = 800
        METERS_PER_MILE = 1609.344

        ############################################################

        database.open()

    .. note:: 
        
        Don't forget to mark this file as executable so that it can be run as a CGI script. You can do this using the chmod command, as described in the What is a CGI script? section earlier in this chapter.

    In this script, we will take the (x, y) coordinate the user clicked on, along with the
    entered search radius, convert the (x, y) coordinate into a longitude and latitude,
    and identify all the place names within that search radius. We then generate a
    high-resolution map showing the shorelines and place names within the search
    radius, and display that map to the user.

    .. note:: 

        Remember that x corresponds to a longitude value, and y to a latitude value.
        
        (x, y) equals (longitude, latitude), not (latitude, longitude).
    
    Let's examine each of these steps in turn.

.. tab:: 英文

    The final CGI script is where the real work is done. Start by creating your
    showResults.py file, and type the following into this file:

    .. code-block:: python

        #!/usr/bin/env python

        import cgi
        import pyproj

        import database
        import mapGenerator

        ############################################################

        MAX_WIDTH = 1000
        MAX_HEIGHT = 800
        METERS_PER_MILE = 1609.344

        ############################################################

        database.open()

    .. note:: 
        
        Don't forget to mark this file as executable so that it can be run as a CGI script. You can do this using the chmod command, as described in the *What is a CGI script?* section earlier in this chapter.

    In this script, we will take the (x, y) coordinate the user clicked on, along with the
    entered search radius, convert the (x, y) coordinate into a longitude and latitude,
    and identify all the place names within that search radius. We then generate a
    high-resolution map showing the shorelines and place names within the search
    radius, and display that map to the user.

    .. note:: 

        Remember that x corresponds to a longitude value, and y to a latitude value.
        
        (x, y) equals (longitude, latitude), not (latitude, longitude).
    
    Let's examine each of these steps in turn.


识别点击点
~~~~~~~~~~~~
Identifying the clicked-on point

.. tab:: 中文

    The selectArea.py script generates an HTML form that is submitted when the user
    clicks on the low-resolution country map. The showResults.py script receives the
    form parameters, including the x and y coordinates of the point the user clicked on.

    By itself, this coordinate isn't very useful. It's simply the x and y offset, measured in
    pixels, of the point the user clicked on. We need to translate the submitted (x, y) pixel
    coordinate into a latitude and longitude value corresponding to the clicked-on point
    on the Earth's surface.

    To do this, we need to have the following information:

    - The map's bounding box in geographic coordinates: *minLong, minLat, maxLong, and maxLat*
    - The map's size in pixels: mapWidth and mapHeight

    These variables were all calculated in the previous section and passed to us using
    hidden form variables, along with the country ID, the desired search radius, and
    the (x, y) coordinate of the clicked on point. We can retrieve all of these using the
    cgi module; add the following code to the end of your showResults.py file:

    .. code-block:: python

        form = cgi.FieldStorage()
        countryID = int(form['countryID'].value)
        radius    = int(form['radius'].value)
        x         = int(form['x'].value)
        y         = int(form['y'].value)

        mapWidth  = int(form['mapWidth'].value)
        mapHeight = int(form['mapHeight'].value)

    With this information, we can now calculate the latitude and longitude that the
    user clicked on. To do this, we first have to calculate the bounds that were used
    to generate the map that the user clicked on. Add the following code to the end
    of your showResults.py file:

    .. code-block:: python

        details = database.get_country_details(countryID)
        envelope = shapely.wkt.loads(details['bounds_wkt'])
        minLong,minLat,maxLong,maxLat = envelope.bounds
        minLong = minLong - 0.2
        minLat = minLat - 0.2
        maxLong = maxLong + 0.2
        maxLat = maxLat + 0.2

    We can now calculate the exact latitude and longitude the user clicked on. We start
    by calculating how far across the image the user clicked, as a number in the range
    from 0 to 1:

    .. code-block:: python

        xFract = float(x)/float(mapWidth)

    An xFract value of 0.0 corresponds to the left side of the image, while an xFract
    value of 1.0 corresponds to the right side of the image. We then combine this with
    the minimum and maximum longitude values to calculate the longitude of the
    clicked-on point:

    .. code-block:: python

        longitude = minLong + xFract * (maxLong-minLong)

    We then do the same to convert the Y coordinate into a latitude value:

    .. code-block:: python

        yFract = float(y)/float(mapHeight)
        latitude = minLat + (1-yFract) * (maxLat-minLat)

    Note that we are using (1-yFract) rather than yFract in the preceding calculation.
    This is because the minLat value refers to the latitude of the bottom of the image, while
    a yFract value of 0.0 corresponds to the top of the image. By using (1-yFract), we
    flip the values vertically so that the latitude is calculated correctly.

.. tab:: 英文

    The selectArea.py script generates an HTML form that is submitted when the user
    clicks on the low-resolution country map. The showResults.py script receives the
    form parameters, including the x and y coordinates of the point the user clicked on.

    By itself, this coordinate isn't very useful. It's simply the x and y offset, measured in
    pixels, of the point the user clicked on. We need to translate the submitted (x, y) pixel
    coordinate into a latitude and longitude value corresponding to the clicked-on point
    on the Earth's surface.

    To do this, we need to have the following information:

    - The map's bounding box in geographic coordinates: *minLong, minLat, maxLong, and maxLat*
    - The map's size in pixels: mapWidth and mapHeight

    These variables were all calculated in the previous section and passed to us using
    hidden form variables, along with the country ID, the desired search radius, and
    the (x, y) coordinate of the clicked on point. We can retrieve all of these using the
    cgi module; add the following code to the end of your showResults.py file:

    .. code-block:: python

        form = cgi.FieldStorage()
        countryID = int(form['countryID'].value)
        radius    = int(form['radius'].value)
        x         = int(form['x'].value)
        y         = int(form['y'].value)

        mapWidth  = int(form['mapWidth'].value)
        mapHeight = int(form['mapHeight'].value)

    With this information, we can now calculate the latitude and longitude that the
    user clicked on. To do this, we first have to calculate the bounds that were used
    to generate the map that the user clicked on. Add the following code to the end
    of your showResults.py file:

    .. code-block:: python

        details = database.get_country_details(countryID)
        envelope = shapely.wkt.loads(details['bounds_wkt'])
        minLong,minLat,maxLong,maxLat = envelope.bounds
        minLong = minLong - 0.2
        minLat = minLat - 0.2
        maxLong = maxLong + 0.2
        maxLat = maxLat + 0.2

    We can now calculate the exact latitude and longitude the user clicked on. We start
    by calculating how far across the image the user clicked, as a number in the range
    from 0 to 1:

    .. code-block:: python

        xFract = float(x)/float(mapWidth)

    An xFract value of 0.0 corresponds to the left side of the image, while an xFract
    value of 1.0 corresponds to the right side of the image. We then combine this with
    the minimum and maximum longitude values to calculate the longitude of the
    clicked-on point:

    .. code-block:: python

        longitude = minLong + xFract * (maxLong-minLong)

    We then do the same to convert the Y coordinate into a latitude value:

    .. code-block:: python

        yFract = float(y)/float(mapHeight)
        latitude = minLat + (1-yFract) * (maxLat-minLat)

    Note that we are using (1-yFract) rather than yFract in the preceding calculation.
    This is because the minLat value refers to the latitude of the bottom of the image, while
    a yFract value of 0.0 corresponds to the top of the image. By using (1-yFract), we
    flip the values vertically so that the latitude is calculated correctly.


按距离识别特征
~~~~~~~~~~~~
Identifying features by distance

.. tab:: 中文

    Let's review what we have achieved so far. The user has selected a country, viewed
    a simple map of the country's outline, entered a desired search radius, and clicked on
    a point on the map to identify the origin for the search. We have then converted this
    clicked-on point into a latitude and longitude value.

    All of this provides us with three numbers: the desired search radius, and the lat/
    long coordinates for the point at which to start the search. Our task now is to identify
    which features are within the given search radius of the clicked-on point:

    .. img:: ./img/268-0.png
       :class: with-border
       :scale: 50
       :align: center

    Because the search radius is specified as an actual distance in miles, we need to be able to calculate distances accurately. We looked at an approach to solving this problem in Chapter 2, GIS, where we considered the concept of a **great circle distance**:

    .. img:: ./img/268-1.png
       :class: with-border
       :scale: 50
       :align: center

    Given a start and end point, the great circle distance calculation tells us the distance
    along the Earth's surface between the two points.

    In order to identify the matching features, we need to somehow find all the
    matching place names which have a great circle distance less than or equal to
    the desired search radius. Let's look at some ways in which we could possibly
    identify these features.

.. tab:: 英文

    Let's review what we have achieved so far. The user has selected a country, viewed
    a simple map of the country's outline, entered a desired search radius, and clicked on
    a point on the map to identify the origin for the search. We have then converted this
    clicked-on point into a latitude and longitude value.

    All of this provides us with three numbers: the desired search radius, and the lat/
    long coordinates for the point at which to start the search. Our task now is to identify
    which features are within the given search radius of the clicked-on point:

    .. img:: ./img/268-0.png
       :class: with-border
       :scale: 50
       :align: center

    Because the search radius is specified as an actual distance in miles, we need to be able to calculate distances accurately. We looked at an approach to solving this problem in Chapter 2, GIS, where we considered the concept of a **great circle distance**:

    .. img:: ./img/268-1.png
       :class: with-border
       :scale: 50
       :align: center

    Given a start and end point, the great circle distance calculation tells us the distance
    along the Earth's surface between the two points.
    
    In order to identify the matching features, we need to somehow find all the
    matching place names which have a great circle distance less than or equal to
    the desired search radius. Let's look at some ways in which we could possibly
    identify these features.

Calculating distances manually
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Calculating distances manually

.. tab:: 中文

    As we saw in Chapter 5, Working with Geospatial Data in Python, pyproj allows us
    to do accurate great circle distance calculations based on two lat/long coordinates,
    like this:

    .. code-block:: python

        geod = pyproj.Geod(ellps='WGS84')
        angle1,angle2,distance = geod.inv(long1, lat1,
                                          long2, lat2)

    The resulting distance is in meters, and we could easily convert this to miles
    as follows:

    .. code-block:: python

        miles = distance / 1609.344

    Based on this, we could write some code to find the features within the desired
    search radius:

    .. code-block:: python

        geod = pyproj.Geod(ellps="WGS84")

        cursor.execute("select id,X(position),Y(position) " +
                       "from places")

        for id,long,lat in cursor:
            angle1,angle2,distance = geod.inv(startLong, startLat,
                                              long, lat)
            if distance / 1609.344 <= searchRadius:
                ...
        
    This would certainly work, and would return an accurate list of all features within
    the given search radius. The problem is speed; because there are more than four
    million features in our places table, this program would take several minutes to
    identify all the matching place names. Obviously this isn't a very practical solution.

.. tab:: 英文

    As we saw in Chapter 5, Working with Geospatial Data in Python, pyproj allows us
    to do accurate great circle distance calculations based on two lat/long coordinates,
    like this:

    .. code-block:: python

        geod = pyproj.Geod(ellps='WGS84')
        angle1,angle2,distance = geod.inv(long1, lat1,
                                          long2, lat2)

    The resulting distance is in meters, and we could easily convert this to miles
    as follows:

    .. code-block:: python

        miles = distance / 1609.344

    Based on this, we could write some code to find the features within the desired
    search radius:

    .. code-block:: python

        geod = pyproj.Geod(ellps="WGS84")

        cursor.execute("select id,X(position),Y(position) " +
                       "from places")

        for id,long,lat in cursor:
            angle1,angle2,distance = geod.inv(startLong, startLat,
                                              long, lat)
            if distance / 1609.344 <= searchRadius:
                ...
        
    This would certainly work, and would return an accurate list of all features within
    the given search radius. The problem is speed; because there are more than four
    million features in our places table, this program would take several minutes to
    identify all the matching place names. Obviously this isn't a very practical solution.


Using angular distances
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Using angular distances

.. tab:: 中文

    We saw an alternative way of identifying features by distance in *Chapter 5, Working
    with Geospatial Data in Python*, where we looked for all parks in or near an urban
    area. In that chapter, we used an **angular distance** to estimate how far apart two
    points were. An angular distance is a distance measured in degrees—technically,
    it is the angle between two rays going out from the center of the Earth through the
    two desired points on the Earth's surface. Because latitude and longitude values
    are angular measurements, we can easily calculate an angular distance based on
    two lat/long values like this:

    .. code-block:: python

        distance = math.sqrt((long2-long1)**2) + (lat2-lat1)**2)

    This is a simple Cartesian distance calculation. We are naively treating lat/long
    values as if they were Cartesian coordinates. This isn't right, but it does give us
    a distance measurement of sorts.

    So what does this angular distance measurement give us? We know that the bigger
    the angular distance, the bigger the real (great circle) distance will be. In Chapter 5,
    Working with Geospatial Data in Python, we used this to identify all parks in California
    which where approximately within ten kilometers of an urban area. However, we
    could get away with this in that chapter because we were only dealing with data for
    California. In reality, the angular distance varies greatly depending on which latitude
    you are dealing with; looking for points within ±1 degree of longitude of your current
    location will include all points within 111 km if you are at the equator, 100 km if you
    are at ±30 degree latitude, 55 km at ±60 degree, and zero km at the poles:

    .. image:: ./img/270-0.png
       :class: with-border
       :scale: 50
       :align: center

    Because DISTAL includes data for the entire world, angular measurements would be all but useless—we can't assume that a given difference in latitude and longitude values would equal a given distance across the Earth's surface in any way which would help us do the distance-based searching.

.. tab:: 英文

    We saw an alternative way of identifying features by distance in *Chapter 5, Working
    with Geospatial Data in Python*, where we looked for all parks in or near an urban
    area. In that chapter, we used an **angular distance** to estimate how far apart two
    points were. An angular distance is a distance measured in degrees—technically,
    it is the angle between two rays going out from the center of the Earth through the
    two desired points on the Earth's surface. Because latitude and longitude values
    are angular measurements, we can easily calculate an angular distance based on
    two lat/long values like this:

    .. code-block:: python

        distance = math.sqrt((long2-long1)**2) + (lat2-lat1)**2)

    This is a simple Cartesian distance calculation. We are naively treating lat/long
    values as if they were Cartesian coordinates. This isn't right, but it does give us
    a distance measurement of sorts.

    So what does this angular distance measurement give us? We know that the bigger
    the angular distance, the bigger the real (great circle) distance will be. In Chapter 5,
    Working with Geospatial Data in Python, we used this to identify all parks in California
    which where approximately within ten kilometers of an urban area. However, we
    could get away with this in that chapter because we were only dealing with data for
    California. In reality, the angular distance varies greatly depending on which latitude
    you are dealing with; looking for points within ±1 degree of longitude of your current
    location will include all points within 111 km if you are at the equator, 100 km if you
    are at ±30 degree latitude, 55 km at ±60 degree, and zero km at the poles:

    .. image:: ./img/270-0.png
       :class: with-border
       :scale: 50
       :align: center

    Because DISTAL includes data for the entire world, angular measurements would be all but useless—we can't assume that a given difference in latitude and longitude values would equal a given distance across the Earth's surface in any way which would help us do the distance-based searching.

Using projected coordinates
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Using projected coordinates

.. tab:: 中文

    Another way of finding all points within a given distance is to use a projected
    coordinate system that accurately represents distance as differences between
    coordinate values. For example, the Universal Transverse Mercator projection
    defines Y coordinates as a number of meters north or south of the equator, and
    X coordinates as a number of meters east or west of a given reference point.
    Using the UTM projection, it would be easy to identify all points within a given
    distance by using the Cartesian distance formula:

    .. code-block:: python

        distance = math.sqrt((long2-long1)**2) + (lat2-lat1)**2)
        if distance < searchRadius:
        ......

    Unfortunately, projected coordinate systems such as UTM are only accurate for
    data that covers a small portion of the Earth's surface. The UTM coordinate system
    is actually a large number of different projections, dividing the world up into sixty
    separate "zones" each six degrees of longitude wide. You need to use the correct
    UTM zone for your particular data: California's coordinates belong in UTM zone
    10, and attempting to project them into UTM zone 20 would cause your distance
    measurements to be very inaccurate.

    If you had data that covered only a small area of the Earth's surface, using
    a projected coordinate system would have great advantages. Not only could
    you calculate distances using Cartesian coordinates, you could also make use
    of database functions such as PostGIS's ST_DWithin() function to quickly find
    all points within a given physical distance of a central point.

    Unfortunately, the DISTAL application makes use of data covering the entire Earth.
    For this reason, we can't use projected coordinates for this application, and have to
    find some other way of solving this problem.

    .. note:: 
        
        Of course, the DISTAL application was deliberately designed to include world-wide data, for precisely this reason. Being able to use a single UTM zone for all the data would be too convenient.

    Actually, there is a way in which DISTAL could use projected UTM coordinates,
    but it's rather complicated. Because every feature in a given database table has to
    have the same spatial reference, it isn't possible to have different features in a table
    belonging to different UTM zones—the only way we could store worldwide data in
    UTM projections would be to have a separate database table for each UTM zone.

    This would require sixty separate database tables! To identify the points within
    a given distance, you would first have to figure out which UTM zone the starting
    point was in, and then check the features within that database table. You would also
    have to deal with searches that extend out beyond the edge of a single UTM zone.

    Needless to say, this approach is far too complex for us. It would work
    (and would scale better than any of the alternatives) but we won't consider
    it because of its complexity.

.. tab:: 英文

    Another way of finding all points within a given distance is to use a projected
    coordinate system that accurately represents distance as differences between
    coordinate values. For example, the Universal Transverse Mercator projection
    defines Y coordinates as a number of meters north or south of the equator, and
    X coordinates as a number of meters east or west of a given reference point.
    Using the UTM projection, it would be easy to identify all points within a given
    distance by using the Cartesian distance formula:

    .. code-block:: python

        distance = math.sqrt((long2-long1)**2) + (lat2-lat1)**2)
        if distance < searchRadius:
        ......

    Unfortunately, projected coordinate systems such as UTM are only accurate for
    data that covers a small portion of the Earth's surface. The UTM coordinate system
    is actually a large number of different projections, dividing the world up into sixty
    separate "zones" each six degrees of longitude wide. You need to use the correct
    UTM zone for your particular data: California's coordinates belong in UTM zone
    10, and attempting to project them into UTM zone 20 would cause your distance
    measurements to be very inaccurate.

    If you had data that covered only a small area of the Earth's surface, using
    a projected coordinate system would have great advantages. Not only could
    you calculate distances using Cartesian coordinates, you could also make use
    of database functions such as PostGIS's ST_DWithin() function to quickly find
    all points within a given physical distance of a central point.

    Unfortunately, the DISTAL application makes use of data covering the entire Earth.
    For this reason, we can't use projected coordinates for this application, and have to
    find some other way of solving this problem.

    .. note:: 
        
        Of course, the DISTAL application was deliberately designed to include world-wide data, for precisely this reason. Being able to use a single UTM zone for all the data would be too convenient.

    Actually, there is a way in which DISTAL could use projected UTM coordinates,
    but it's rather complicated. Because every feature in a given database table has to
    have the same spatial reference, it isn't possible to have different features in a table
    belonging to different UTM zones—the only way we could store worldwide data in
    UTM projections would be to have a separate database table for each UTM zone.

    This would require sixty separate database tables! To identify the points within
    a given distance, you would first have to figure out which UTM zone the starting
    point was in, and then check the features within that database table. You would also
    have to deal with searches that extend out beyond the edge of a single UTM zone.
    
    Needless to say, this approach is far too complex for us. It would work
    (and would scale better than any of the alternatives) but we won't consider
    it because of its complexity.

A hybrid approach
^^^^^^^^^^^^^^^^^^^
A hybrid approach

.. tab:: 中文

    In Chapter 6, GIS in the Database, we looked at the process of identifying all points
    within a given polygon. Because MySQL only handles bounding-box intersection
    tests, we ended up having to write a program which asked the database to identify
    all points within the bounding box, and then manually checked each point to see if
    it was actually inside the polygon:

    .. image:: ./img/272-0.png
       :class: with-border
       :scale: 50
       :align: center

    This suggests a way in which we can solve the distance-based-selection problem for
    DISTAL: we can calculate a bounding box which encloses the desired search radius,
    ask the database to identify all points within that bounding box, and then calculate
    the great circle distance for all the returned points, selecting just those points that are
    actually inside the search radius. Because a relatively small number of points will
    be inside the bounding box, calculating the great circle distance for just these points
    will be quick, allowing us to accurately find the matching points without a large
    performance penalty.

    Let's start by calculating the bounding box. We already know the coordinates for the
    starting point and the desired search radius:

    .. image:: ./img/273-0.png
       :class: with-border
       :scale: 50
       :align: center

    Using pyproj, we can calculate the lat/long coordinates for four points by traveling
    radius meters directly north, south, east, and west of the starting point:

    .. image:: ./img/273-1.png
       :class: with-border
       :scale: 50
       :align: center

    We then use these four points to define the bounding box that encloses the desired
    search radius:

    .. image:: ./img/274-0.png
       :class: with-border
       :scale: 50
       :align: center

    We're going to create a new function within our database module, which performs a
    spatial search using this bounding box. Let's start by adding the following to the end
    of your database.py module:

    .. code-block:: python

        def find_places_within(startLat, startLong, searchRadius):
            global _cursor

            if DB_TYPE == "MySQL":
                ...
            elif DB_TYPE == "PostGIS":
                ...
            elif DB_TYPE == "SpatiaLite":
                ...

    Note that, because we're using pyproj to do a forward geodetic calculation, we
    can calculate the correct lat/long coordinates for the bounding box regardless of
    the latitude of the starting point. The only place this will fail is if startLat is within
    searchRadius meters of the North or South Pole—which is highly unlikely given
    that we're searching for cities (and we could always add error-checking code to
    catch this).

    When it's finished, our find_places_within() function will return a list of
    all the places within the given bounding box, as well as the calculated bounding
    box. Because the spatial queries are different for each database, we'll look at each
    one individually.

    For MySQL, we'll create a Polygon out of the supplied bounding box, and then use
    the MBRContains() function to search for places within that Polygon. To do this,
    replace the first ... with the following code:

    .. code-block:: python

        p = Polygon([(minLong, minLat), (maxLong, minLat),
            (maxLong, maxLat), (minLong, maxLat),
            (minLong, minLat)])
        wkt = shapely.wkt.dumps(p)
        _cursor.execute("SELECT name," +
                        "X(position),Y(position) " +
                        "FROM places WHERE MBRContains(" +
                        "GeomFromText(%s), position)", (wkt,))

    PostGIS uses a similar approach, creating a Polygon and then using the
    ST_CONTAINS() function to identify the matching places:

    .. code-block:: python

        p = Polygon([(minLong, minLat), (maxLong, minLat),
                     (maxLong, maxLat), (minLong, maxLat),
                     (minLong, minLat)])
        wkt = shapely.wkt.dumps(p)
        _cursor.execute("SELECT name," +
                        "ST_X(position),ST_Y(position) " +
                        "FROM places WHERE ST_CONTAINS(" +
                        "ST_GeomFromText(%s, 4326), " +
            "position)", (wkt,))

    .. note::
        
        You might be wondering why we don't use PostGIS's ST_DWITHIN() function to identify the matching places. The problem is that we are using unprojected coordinates, which means that the "distance" supplied to ST_DWITHIN() would have to be in measured in degrees rather than meters. This is possible, but there are some tricky calculations required to convert from meters to degrees. To keep things simple, we'll use the ST_CONTAINS() function instead.

    Finally, for SpatiaLite we have to do a bit more work. Remember that SpatiaLite
    doesn't automatically use a spatial index for queries. To make this code efficient
    in SpatiaLite, we have to check the spatial index directly:

    .. code-block:: python

        _cursor.execute("SELECT name," +
                        "X(position),Y(position) " +
                        "FROM places WHERE id in " +
                        "(SELECT pkid " +
                        "FROM idx_places_position " +
                        "WHERE xmin >= ? AND xmax <= ? " +
                        "AND ymin >= ? and ymax <= ?)",
                        (minLong, maxLong, minLat, maxLat))

    Now that we have executed an SQL query to identify all the points within the
    bounding box, we can check the great circle distance and discard those points,
    which are inside the bounding box, but outside the search radius. To do this,
    add the following to the end of your find_places_within() function:

    .. code-block:: python

        places = [] # List of (long, lat, name) tuples.

        geod = pyproj.Geod(ellps="WGS84")

        for row in _cursor:
            name,long,lat = row
            angle1,angle2,distance = geod.inv(startLong, startLat,
                                              long, lat)
            if distance > searchRadius: continue

            places.append([long, lat, name])

        return {'places' : places,
                'minLat' : minLat,
                'minLong' : minLong,
                'maxLat' : maxLat,
                'maxLong' : maxLong }

    As you can see, we return the list of matching places, along with the minimum and
    maximum latitude and longitude values we calculated.

    This completes our *find_places_within()* function, which achieves a 100 percent
    accurate distance-based lookup on place names, with the results taking only a
    fraction of a second to calculate.

.. tab:: 英文

    In Chapter 6, GIS in the Database, we looked at the process of identifying all points
    within a given polygon. Because MySQL only handles bounding-box intersection
    tests, we ended up having to write a program which asked the database to identify
    all points within the bounding box, and then manually checked each point to see if
    it was actually inside the polygon:

    .. image:: ./img/272-0.png
       :class: with-border
       :scale: 50
       :align: center

    This suggests a way in which we can solve the distance-based-selection problem for
    DISTAL: we can calculate a bounding box which encloses the desired search radius,
    ask the database to identify all points within that bounding box, and then calculate
    the great circle distance for all the returned points, selecting just those points that are
    actually inside the search radius. Because a relatively small number of points will
    be inside the bounding box, calculating the great circle distance for just these points
    will be quick, allowing us to accurately find the matching points without a large
    performance penalty.

    Let's start by calculating the bounding box. We already know the coordinates for the
    starting point and the desired search radius:

    .. image:: ./img/273-0.png
       :class: with-border
       :scale: 50
       :align: center

    Using pyproj, we can calculate the lat/long coordinates for four points by traveling
    radius meters directly north, south, east, and west of the starting point:

    .. image:: ./img/273-1.png
       :class: with-border
       :scale: 50
       :align: center

    We then use these four points to define the bounding box that encloses the desired
    search radius:

    .. image:: ./img/274-0.png
       :class: with-border
       :scale: 50
       :align: center

    We're going to create a new function within our database module, which performs a
    spatial search using this bounding box. Let's start by adding the following to the end
    of your database.py module:

    .. code-block:: python

        def find_places_within(startLat, startLong, searchRadius):
            global _cursor

            if DB_TYPE == "MySQL":
                ...
            elif DB_TYPE == "PostGIS":
                ...
            elif DB_TYPE == "SpatiaLite":
                ...

    Note that, because we're using pyproj to do a forward geodetic calculation, we
    can calculate the correct lat/long coordinates for the bounding box regardless of
    the latitude of the starting point. The only place this will fail is if startLat is within
    searchRadius meters of the North or South Pole—which is highly unlikely given
    that we're searching for cities (and we could always add error-checking code to
    catch this).

    When it's finished, our find_places_within() function will return a list of
    all the places within the given bounding box, as well as the calculated bounding
    box. Because the spatial queries are different for each database, we'll look at each
    one individually.

    For MySQL, we'll create a Polygon out of the supplied bounding box, and then use
    the MBRContains() function to search for places within that Polygon. To do this,
    replace the first ... with the following code:

    .. code-block:: python

        p = Polygon([(minLong, minLat), (maxLong, minLat),
            (maxLong, maxLat), (minLong, maxLat),
            (minLong, minLat)])
        wkt = shapely.wkt.dumps(p)
        _cursor.execute("SELECT name," +
                        "X(position),Y(position) " +
                        "FROM places WHERE MBRContains(" +
                        "GeomFromText(%s), position)", (wkt,))

    PostGIS uses a similar approach, creating a Polygon and then using the
    ST_CONTAINS() function to identify the matching places:

    .. code-block:: python

        p = Polygon([(minLong, minLat), (maxLong, minLat),
                     (maxLong, maxLat), (minLong, maxLat),
                     (minLong, minLat)])
        wkt = shapely.wkt.dumps(p)
        _cursor.execute("SELECT name," +
                        "ST_X(position),ST_Y(position) " +
                        "FROM places WHERE ST_CONTAINS(" +
                        "ST_GeomFromText(%s, 4326), " +
            "position)", (wkt,))

    .. note::
        
        You might be wondering why we don't use PostGIS's ST_DWITHIN() function to identify the matching places. The problem is that we are using unprojected coordinates, which means that the "distance" supplied to ST_DWITHIN() would have to be in measured in degrees rather than meters. This is possible, but there are some tricky calculations required to convert from meters to degrees. To keep things simple, we'll use the ST_CONTAINS() function instead.

    Finally, for SpatiaLite we have to do a bit more work. Remember that SpatiaLite
    doesn't automatically use a spatial index for queries. To make this code efficient
    in SpatiaLite, we have to check the spatial index directly:

    .. code-block:: python

        _cursor.execute("SELECT name," +
                        "X(position),Y(position) " +
                        "FROM places WHERE id in " +
                        "(SELECT pkid " +
                        "FROM idx_places_position " +
                        "WHERE xmin >= ? AND xmax <= ? " +
                        "AND ymin >= ? and ymax <= ?)",
                        (minLong, maxLong, minLat, maxLat))

    Now that we have executed an SQL query to identify all the points within the
    bounding box, we can check the great circle distance and discard those points,
    which are inside the bounding box, but outside the search radius. To do this,
    add the following to the end of your find_places_within() function:

    .. code-block:: python

        places = [] # List of (long, lat, name) tuples.

        geod = pyproj.Geod(ellps="WGS84")

        for row in _cursor:
            name,long,lat = row
            angle1,angle2,distance = geod.inv(startLong, startLat,
                                              long, lat)
            if distance > searchRadius: continue

            places.append([long, lat, name])

        return {'places' : places,
                'minLat' : minLat,
                'minLong' : minLong,
                'maxLat' : maxLat,
                'maxLong' : maxLong }

    As you can see, we return the list of matching places, along with the minimum and
    maximum latitude and longitude values we calculated.

    This completes our *find_places_within()* function, which achieves a 100 percent
    accurate distance-based lookup on place names, with the results taking only a
    fraction of a second to calculate.


显示结果
~~~~~~~~~~~~
Displaying the results

.. tab:: 中文

    Now that we have calculated the list of place names within the desired search radius,
    we can use the mapGenerator.py module to display them. Before we do so, though,
    we'll have to set up a data source to display the high-resolution shorelines. Let's add
    another function to our database.py module, which does this:

    .. code-block:: python

        def get_shoreline_datasource():
            if DB_TYPE == "MySQL":

                vrtFile = os.path.join(os.path.dirname(__file__),
                                       "shorelines.vrt")
                f = file(vrtFile, "w")
                f.write('<OGRVRTDataSource>\n')
                f.write(' <OGRVRTLayer name="shorelines">\n')
                f.write('
                <SrcDataSource>MYSQL:' + MYSQL_DBNAME)
                if MYSQL_USERNAME not in ["", None]:
                    f.write(",user=" + MYSQL_USERNAME)
                if MYSQL_PASSWORD not in ["", None]:
                    f.write(",passwd=" + MYSQL_PASSWORD)
                f.write(',tables=shorelines</SrcDataSource>\n')
                f.write('   <SrcSQL>\n')
                f.write('     SELECT id,outline FROM shorelines ' +
                                                'WHERE level=1\n')
                f.write('     </SrcSQL>\n')
                f.write(' </OGRVRTLayer>\n')
                f.write('</OGRVRTDataSource>\n')
                f.close()

                return {'type' : "OGR",
                        'file' : vrtFile,
                        'layer' : "shorelines"}
            elif DB_TYPE == "PostGIS":
                return {'type': "PostGIS",
                        'dbname': "distal",
                        'table': "shorelines",
                        'user': POSTGIS_USERNAME,
                        'password' : POSTGIS_PASSWORD}
            elif DB_TYPE == "SpatiaLite":
                return {'type': "SQLite",
                        'file': SPATIALITE_DBNAME,
                        'table': "shorelines",
                        'geometry_field' : "outline",
                        'key_field': "id"}

    As you can see, this is almost identical to our *get_country_datasource()* function, except that it accesses a different database table to display the high-resolution shoreline rather than the low-resolution country outlines.

    .. note::

        Notice that the SrcSQL statement in our .VRT file only includes shoreline data where level is equal 1. This means that we're only displaying the coastlines, and not the lakes, islands-on-lakes, and so on. Because the mapGenerator.py module doesn't support multiple data sources, we aren't able to draw lakes in this version of the DISTAL system. Extending mapGenerator.py to support multiple data sources is possible, but is too complicated for this chapter. For now we'll just have to live with this limitation.

    With this in place, we can finally return to our showResults.py file and use it to display our results:

    .. code-block:: python

        results = database.find_places_within(latitude, longitude,
                                              radius)

        imgFile = mapGenerator.generateMap(datasource,
                                           minLong, minLat,
                                           maxLong, maxLat,
                                           600, 600,
                                           points=results['places'])

    When we called the map generator previously, we used a filter expression to
    highlight particular features. In this case we don't need to highlight anything.
    Instead, we pass it the list of place names to display on the map in the keyword
    parameter named points.

    The map generator creates a PNG-format file, and returns a reference to that file
    which we can then display to the user:

    .. code-block:: python

        print 'Content-Type: text/html; charset=UTF-8\n\n'
        print '<html>'
        print '<head><title>Search Results</title></head>'
        print '<body>'
        print '<b>' + countryName + '</b>'
        print '<p>'
        print '<img src="' + imgFile + '">'
        print '</body>'
        print '</html>'

    This completes our first version of the *showResults.py* CGI script.

.. tab:: 英文

    Now that we have calculated the list of place names within the desired search radius,
    we can use the mapGenerator.py module to display them. Before we do so, though,
    we'll have to set up a data source to display the high-resolution shorelines. Let's add
    another function to our database.py module, which does this:

    .. code-block:: python

        def get_shoreline_datasource():
            if DB_TYPE == "MySQL":

                vrtFile = os.path.join(os.path.dirname(__file__),
                                       "shorelines.vrt")
                f = file(vrtFile, "w")
                f.write('<OGRVRTDataSource>\n')
                f.write(' <OGRVRTLayer name="shorelines">\n')
                f.write('
                <SrcDataSource>MYSQL:' + MYSQL_DBNAME)
                if MYSQL_USERNAME not in ["", None]:
                    f.write(",user=" + MYSQL_USERNAME)
                if MYSQL_PASSWORD not in ["", None]:
                    f.write(",passwd=" + MYSQL_PASSWORD)
                f.write(',tables=shorelines</SrcDataSource>\n')
                f.write('   <SrcSQL>\n')
                f.write('     SELECT id,outline FROM shorelines ' +
                                                'WHERE level=1\n')
                f.write('     </SrcSQL>\n')
                f.write(' </OGRVRTLayer>\n')
                f.write('</OGRVRTDataSource>\n')
                f.close()

                return {'type' : "OGR",
                        'file' : vrtFile,
                        'layer' : "shorelines"}
            elif DB_TYPE == "PostGIS":
                return {'type': "PostGIS",
                        'dbname': "distal",
                        'table': "shorelines",
                        'user': POSTGIS_USERNAME,
                        'password' : POSTGIS_PASSWORD}
            elif DB_TYPE == "SpatiaLite":
                return {'type': "SQLite",
                        'file': SPATIALITE_DBNAME,
                        'table': "shorelines",
                        'geometry_field' : "outline",
                        'key_field': "id"}

    As you can see, this is almost identical to our *get_country_datasource()* function, except that it accesses a different database table to display the high-resolution shoreline rather than the low-resolution country outlines.

    .. note::

        Notice that the SrcSQL statement in our .VRT file only includes shoreline data where level is equal 1. This means that we're only displaying the coastlines, and not the lakes, islands-on-lakes, and so on. Because the mapGenerator.py module doesn't support multiple data sources, we aren't able to draw lakes in this version of the DISTAL system. Extending mapGenerator.py to support multiple data sources is possible, but is too complicated for this chapter. For now we'll just have to live with this limitation.

    With this in place, we can finally return to our showResults.py file and use it to display our results:

    .. code-block:: python

        results = database.find_places_within(latitude, longitude,
                                              radius)

        imgFile = mapGenerator.generateMap(datasource,
                                           minLong, minLat,
                                           maxLong, maxLat,
                                           600, 600,
                                           points=results['places'])

    When we called the map generator previously, we used a filter expression to
    highlight particular features. In this case we don't need to highlight anything.
    Instead, we pass it the list of place names to display on the map in the keyword
    parameter named points.

    The map generator creates a PNG-format file, and returns a reference to that file
    which we can then display to the user:

    .. code-block:: python

        print 'Content-Type: text/html; charset=UTF-8\n\n'
        print '<html>'
        print '<head><title>Search Results</title></head>'
        print '<body>'
        print '<b>' + countryName + '</b>'
        print '<p>'
        print '<img src="' + imgFile + '">'
        print '</body>'
        print '</html>'

    This completes our first version of the *showResults.py* CGI script.

