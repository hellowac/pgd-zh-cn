选择要编辑的要素
============================================

Selecting a feature to edit

.. tab:: 中文

    As we discussed in the section on designing the ShapeEditor, GeoDjango's built-in
    map widgets can only display a single feature at a time. In order to display a map
    with all the shapefile's features on it, we will have to use OpenLayers directly, along
    with a Tile Map Server and a custom AJAX-based click handler. The basic workflow
    will look like this:

    .. image:: ./img/441-0.png
       :align: center

    Let's start by implementing the Tile Map Server, and then see what's involved in using OpenLayers, along with a custom click-handler and some server-side AJAX code, to respond when the user clicks on the map.

.. tab:: 英文

    As we discussed in the section on designing the ShapeEditor, GeoDjango's built-in
    map widgets can only display a single feature at a time. In order to display a map
    with all the shapefile's features on it, we will have to use OpenLayers directly, along
    with a Tile Map Server and a custom AJAX-based click handler. The basic workflow
    will look like this:

    .. image:: ./img/441-0.png
       :align: center

    Let's start by implementing the Tile Map Server, and then see what's involved in using OpenLayers, along with a custom click-handler and some server-side AJAX code, to respond when the user clicks on the map.

实现 Tile 地图服务器
--------------------------------------
Implementing Tile Map Server

.. tab:: 中文

    As we discussed in Bonus chapter, Web Frameworks for Python Geospatial Development
    (Download link available in preface) the Tile Map Server Protocol is a simple
    RESTful protocol for serving map tiles. The TMS protocol includes calls to identify
    the various maps which can be displayed, along with information about the available
    map tiles, as well as providing access to the map tile images themselves.

    Let's briefly review the terminology used by the TMS protocol:
    
    - A **Tile Map Server** is the overall web server which is implementing the TMS protocol.
    - A **Tile Map Service** provides access to a particular set of maps. There can be multiple Tile Map Services hosted by a single Tile Map Server.
    - A **Tile Map** is a complete map of all or part of the Earth's surface, displaying a particular set of features or styled in a particular way. A Tile Map Service can provide access to more than one Tile Map.
    - A **Tile Set** is a collection of tiles displaying a given Tile Map at a given zoom level.
    - A **Tile** is a single map image representing a small portion of the map being displayed by the Tile Set.
    
    This may sound confusing, but it's actually not too bad. We'll be implementing a Tile
    Map Server with just one Tile Map Service, which we'll call the "ShapeEditor Tile Map
    Service". There will be one Tile Map for each shapefile that has been uploaded, and
    we'll support Tile Sets for a standard range of zoom levels. Finally, we'll use Mapnik
    to render the individual Tiles within the Tile Set.

    Following the Django principle of breaking a large and complex system down
    into separate self-contained applications, we will implement the Tile Map Server
    as a separate application within the shapeEditor project. Start by cd'ing into the
    shapeEditor project directory and type the following:

        python manage.py startapp tms

    This creates our tms application in the top-level directory, making it a reusable
    application. Move the newly-created directory into the shapeEditor sub-directory,
    either using the mouse or by typing the following command::

        mv tms shapeEditor

    This makes the Tile Map Server specific to our project. We then have to enable the
    application by editing our project's settings.py module and adding the following
    entry to the end of the INSTALLED_APPS list::

        'shapeEditor.tms',

    Next, we want to make our Tile Map Server's URLs available as part of the
    shapeEditor project. To do this, edit the global urls.py module (located inside
    the main shapeEditor directory), and add the following highlighted line to the
    first urlpatterns = ... statement::

        urlpatterns = patterns('',
        url(r'^editor/', include('shapeEditor.editor.urls')),
        url(r'^tms/', include('shapeEditor.tms.urls')),
        )

    We now want to define the individual URLs provided by our Tile Map Server
    application. To do this, create a new module named urls.py inside the tms
    directory, and enter the following into this module::

        # URLConf for the shapeEditor.tms application.

        from django.conf.urls import patterns, url

        urlpatterns = patterns('shapeEditor.tms.views',
            url(r'^$',
                 'root'), # "/tms" calls root()
            url(r'^(?P<version>[0-9.]+)$',
                 'service'), # eg, "/tms/1.0" calls service(version="1.0")
            url(r'^(?P<version>[0-9.]+)/' +
                r'(?P<shapefile_id>\d+)$',
                'tileMap'), # eg, "/tms/1.0/2" calls
                            # tileMap(version="1.0", shapefile_id=2)
            url(r'^(?P<version>[0-9.]+)/' +
                r'(?P<shapefile_id>\d+)/(?P<zoom>\d+)/' +
                r'(?P<x>\d+)/(?P<y>\d+)\.png$',
                'tile'), # eg, "/tms/1.0/2/3/4/5" calls
            # tile(version="1.0", shapefile_id=2, zoom=3, x=4, y=5)
        )

    These URL patterns are more complicated than those we've used in the past,
    because we're now extracting parameters from the URL. For example, consider
    the following URL::

        http://127.0.0.1:8000/tms/1.0

    This will be matched by the second regular expression in our tms application's
    urls.py module::

        ^(?P<version>[0-9.]+)$

    This regular expression will extract the 1.0 portion of the URL and assign it to a
    parameter named version. This parameter is then passed on to the view function
    associated with this URL pattern, as follows::

        tileMap(version="1.0")

    In this way, each of our URL patterns maps an incoming RESTful URL to the
    appropriate view function within our tms application. The included comments
    give examples of how the regular expressions will map to the view functions.

    Let's now set up these view functions. Edit the views.py module inside the tms
    directory, and add the following to this module::

        from django.http import HttpResponse

        def root(request):
            return HttpResponse("Tile Map Server")
        
        def service(request, version):
            return HttpResponse("Tile Map Service")

        def tileMap(request, version, shapefile_id):
            return HttpResponse("Tile Map")

        def tile(request, version, shapefile_id, zoom, x, y):
            return HttpResponse("Tile")

    Obviously these are only placeholder view functions, but they give us the basic
    structure for our Tile Map Server.

    To test that this works, launch the ShapeEditor server by running the
    python manage.py runserver command, and point your web browser to
    http://127.0.0.1:8000/tms. You should see the text you entered into your
    placeholder root() view function.

    Let's make that top-level view function do something useful. Go back to the tms
    application's views.py module, and change the root() function to look as follows::

        def root(request):
            try:
                baseURL = request.build_absolute_uri()
                xml = []
                xml.append('<?xml version="1.0" encoding="utf-8" ?>')
                xml.append('<Services>')
                xml.append(' <TileMapService ' +
                           'title="ShapeEditor Tile Map Service" ' +
                           'version="1.0" href="' + baseURL + '/1.0"/>')
                xml.append('</Services>')
                return HttpResponse("\n".join(xml), mimetype="text/xml")
            except:

                traceback.print_exc()
                return HttpResponse("Error")

    You'll also need to add the following import statement to the top of the module::
    
        import traceback

    This view function returns an XML-format response describing the one-and-only Tile
    Map Service supported by our TMS server. This Tile Map Service is identified by a
    version number, 1.0 (Tile Map Services are typically identified by version number).
    If you now go to http://127.0.0.1:8000/tms, you'll see the TMS response
    displayed in your web browser:

    .. image:: ./img/445-0.png
       :align: center

    As you can see, this provides a list of the Tile Map Services which this TMS server provides. OpenLayers will use this to access our Tile Map Service.

    .. note::

        Error handling

        Notice that we've wrapped our TMS view function in a try...
        except statement, and used the traceback standard library
        module to print out the exception if anything goes wrong.
        We're doing this because our code will be called directly by
        OpenLayers using AJAX; Django helpfully handles exceptions
        and returns an HTML error page to the caller, but in this case
        OpenLayers won't display that page if there is an error in your
        code. Instead, all you'll see are broken image icons instead of a
        map, and the error itself will remain a mystery.
        By wrapping our Python code in a try...except statement,
        we can catch any exceptions in our Python code and print them
        out. This will cause the error to appear in Django's web server
        log, so we can see what went wrong. This is a useful technique
        to use whenever you write AJAX request handlers in Python.

    We're now ready to implement the Tile Map Service itself. Edit the view.py module
    again, and change the service() function to look like this::

        def service(request, version):
            try:
                if version != "1.0":
                    raise Http404

                baseURL = request.build_absolute_uri()
                xml = []
                xml.append('<?xml version="1.0" encoding="utf-8" ?>')
                xml.append('<TileMapService version="1.0" services="' +
                            baseURL + '">')
                xml.append(' <Title>ShapeEditor Tile Map Service' +
                           '</Title>')
                xml.append(' <Abstract></Abstract>')
                xml.append(' <TileMaps>')
                for shapefile in Shapefile.objects.all():
                    id = str(shapefile.id)
                    xml.append('    <TileMap title="' +
                                shapefile.filename + '"')
                    xml.append('        srs="EPSG:4326"')
                    xml.append('        href="'+baseURL+'/'+id+'"/>')
                xml.append(' </TileMaps>')
                xml.append('</TileMapService>')
                return HttpResponse("\n".join(xml), mimetype="text/xml")

            except:
                traceback.print_exc()
                return HttpResponse("Error")
    
    You'll also need to add the following import statements to the top of the module::

        from django.http import Http404
        from geodjango.shapeEditor.models import Shapefile

    Notice that this function raises an Http404 exception if the version number is wrong.
    This exception tells Django to return a HTTP 404 error, which is the standard error
    response when an incorrect URL has been used.

    Assuming the version number is correct, we iterate over the various Shapefile
    objects in the database, listing each uploaded shapefile as a Tile Map.

    If you save this file and enter http://127.0.0.1:8000/tms/1.0 into your web
    browser, you should see a list of the available tile maps, in XML format:

    .. image:: ./img/447-0.png
       :align: center

    We next need to implement the *tileMap()* function to display the various Tile Sets
    available for a given Tile Map. Before we can do this, though, we're going to have to
    learn a bit about the notion of zoom levels.

    As we have seen, a slippy map lets the user zoom in and out to view the map's
    contents. This zooming is done by controlling the map's zoom level. Typically,
    a zoom level is specified as a simple number: zoom level zero is when the map
    is fully zoomed out, zoom level 1 is when the map is zoomed in once, and so on.

    Let's start by considering the map when it is zoomed out completely (in other words,
    zoom level 0). In this case, we want the entire Earth's surface to be covered by just
    two map tiles:

    .. image:: ./img/448-0.png
       :align: center

    Each map tile at this zoom level would cover 180 degrees of latitude and longitude.
    If each tile was 256 pixels square, this would mean that each pixel would cover 180
    / 256 = 0.703125 map units, where in this case a "map unit" is a degree of latitude or
    longitude. This number is going to be very important when it comes to calculating
    the Tile Maps.

    Now, whenever we zoom in (for example by going from zoom level 0 to zoom level
    1), the width and height of the visible area is halved. For example, at zoom level 1
    the Earth's surface would be displayed as the following series of eight tiles:

    .. image:: ./img/448-1.png
       :align: center

    Following this pattern, we can calculate the number of map units covered by a single
    pixel on the map, for a given zoom level, using the following formula:

    .. image:: ./img/449-0.png
       :align: center

    Since we'll be using this formula in our TMS server, let's go ahead and add the
    following code to the end of our tms.py module::

        def _unitsPerPixel(zoomLevel):
            return 0.703125 / math.pow(2, zoomLevel)

    .. note::

        Notice that we start the function name with an underscore; this is a standard Python convention for naming "private" functions within a module.

    You'll also need to add an import math statement to the top of the file.

    Next, we need to add some constants to the top of the module to define the size of
    each map tile, and how many zoom levels we support::
    
        MAX_ZOOM_LEVEL = 10
        TILE_WIDTH     = 256
        TILE_HEIGHT    = 256

    With all this, we're finally ready to implement the tileMap() function to return
    information about the available Tile Sets for a given shapefile's Tile Map. Edit this
    function to look as follows::

        def tileMap(request, version, shapefile_id):
            if version != "1.0":
                raise Http404

            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                raise Http404
            
            try:
                baseURL = request.build_absolute_uri()
                xml = []
                xml.append('<?xml version="1.0" encoding="utf-8" ?>')
                xml.append('<TileMap version="1.0" ' +
                           'tilemapservice="' + baseURL + '">')
                xml.append(' <Title>' + shapefile.filename + '</Title>')
                xml.append(' <Abstract></Abstract>')
                xml.append(' <SRS>EPSG:4326</SRS>')
                xml.append(' <BoundingBox minx="-180" miny="-90" ' +
                           'maxx="180" maxy="90"/>')
                xml.append(' <Origin x="-180" y="-90"/>')
                xml.append(' <TileFormat width="' + str(TILE_WIDTH) +
                           '" height="' + str(TILE_HEIGHT) + '" ' +
                           'mime-type="image/png" extension="png"/>')
                xml.append(' <TileSets profile="global-geodetic">')
                for zoomLevel in range(0, MAX_ZOOM_LEVEL+1):
                    unitsPerPixel = _unitsPerPixel(zoomLevel)
                    xml.append('    <TileSet href="' +
                                baseURL + '/' + str(zoomLevel) +
                                '" units-per-pixel="'+str(unitsPerPixel) +
                                '" order="' + str(zoomLevel) + '"/>')
                xml.append(' </TileSets>')
                xml.append('</TileMap>')
                return HttpResponse("\n".join(xml), mimetype="text/xml")
            except:
                traceback.print_exc()
                return HttpResponse("Error")

    As you can see, we start with some basic error checking on the version and shapefile
    ID, and then iterate through the available zoom levels to provide information about
    the available Tile Sets.

    If you save your changes and enter http://127.0.0.1:8000/tms/1.0/2 into your
    web browser, you should see the following information about the Tile Map for the
    shapefile object with record ID 2:

    .. image:: ./img/451-0.png
       :align: center

    Notice that we provide a total of eleven zoom levels, from zero to ten, with an
    appropriately-calculated units-per-pixel value for each zoom level.

    We have now implemented three of the four view functions required to implement
    our own Tile Map Server. For the final function, tile(), we are going to write
    our own tile renderer. The tile() function accepts a Tile Map Service version,
    a shapefile ID, a zoom level, and the X and Y coordinates for the desired tile::
    
        def tile(request, version, shapefile_id, zoom, x, y):
        ...
    
    This function needs to generate the appropriate map tile and return the rendered
    image back to the caller. Before we implement this function, let's take a step back
    and think about what the map rendering should look like.

    We want the map to include the outline of the various features within the given
    shapefile. However, by themselves these features won't look very meaningful:

    .. image:: ./img/452-0.png
       :align: center

    It isn't until these features are shown in context, by displaying a **base map** behind the
    features, that we can see what they are supposed to represent:

    .. image:: ./img/452-1.png
       :align: center

    Because of this, we're going to have to display a base map on which the features
    themselves are drawn. Let's build that base map, and then we can use this, along
    with the shapefile's features, to render the map tiles.

.. tab:: 英文

    As we discussed in Bonus chapter, Web Frameworks for Python Geospatial Development
    (Download link available in preface) the Tile Map Server Protocol is a simple
    RESTful protocol for serving map tiles. The TMS protocol includes calls to identify
    the various maps which can be displayed, along with information about the available
    map tiles, as well as providing access to the map tile images themselves.

    Let's briefly review the terminology used by the TMS protocol:
    
    - A **Tile Map Server** is the overall web server which is implementing the TMS protocol.
    - A **Tile Map Service** provides access to a particular set of maps. There can be multiple Tile Map Services hosted by a single Tile Map Server.
    - A **Tile Map** is a complete map of all or part of the Earth's surface, displaying a particular set of features or styled in a particular way. A Tile Map Service can provide access to more than one Tile Map.
    - A **Tile Set** is a collection of tiles displaying a given Tile Map at a given zoom level.
    - A **Tile** is a single map image representing a small portion of the map being displayed by the Tile Set.
    
    This may sound confusing, but it's actually not too bad. We'll be implementing a Tile
    Map Server with just one Tile Map Service, which we'll call the "ShapeEditor Tile Map
    Service". There will be one Tile Map for each shapefile that has been uploaded, and
    we'll support Tile Sets for a standard range of zoom levels. Finally, we'll use Mapnik
    to render the individual Tiles within the Tile Set.

    Following the Django principle of breaking a large and complex system down
    into separate self-contained applications, we will implement the Tile Map Server
    as a separate application within the shapeEditor project. Start by cd'ing into the
    shapeEditor project directory and type the following:

        python manage.py startapp tms

    This creates our tms application in the top-level directory, making it a reusable
    application. Move the newly-created directory into the shapeEditor sub-directory,
    either using the mouse or by typing the following command::

        mv tms shapeEditor

    This makes the Tile Map Server specific to our project. We then have to enable the
    application by editing our project's settings.py module and adding the following
    entry to the end of the INSTALLED_APPS list::

        'shapeEditor.tms',

    Next, we want to make our Tile Map Server's URLs available as part of the
    shapeEditor project. To do this, edit the global urls.py module (located inside
    the main shapeEditor directory), and add the following highlighted line to the
    first urlpatterns = ... statement::

        urlpatterns = patterns('',
        url(r'^editor/', include('shapeEditor.editor.urls')),
        url(r'^tms/', include('shapeEditor.tms.urls')),
        )

    We now want to define the individual URLs provided by our Tile Map Server
    application. To do this, create a new module named urls.py inside the tms
    directory, and enter the following into this module::

        # URLConf for the shapeEditor.tms application.

        from django.conf.urls import patterns, url

        urlpatterns = patterns('shapeEditor.tms.views',
            url(r'^$',
                 'root'), # "/tms" calls root()
            url(r'^(?P<version>[0-9.]+)$',
                 'service'), # eg, "/tms/1.0" calls service(version="1.0")
            url(r'^(?P<version>[0-9.]+)/' +
                r'(?P<shapefile_id>\d+)$',
                'tileMap'), # eg, "/tms/1.0/2" calls
                            # tileMap(version="1.0", shapefile_id=2)
            url(r'^(?P<version>[0-9.]+)/' +
                r'(?P<shapefile_id>\d+)/(?P<zoom>\d+)/' +
                r'(?P<x>\d+)/(?P<y>\d+)\.png$',
                'tile'), # eg, "/tms/1.0/2/3/4/5" calls
            # tile(version="1.0", shapefile_id=2, zoom=3, x=4, y=5)
        )

    These URL patterns are more complicated than those we've used in the past,
    because we're now extracting parameters from the URL. For example, consider
    the following URL::

        http://127.0.0.1:8000/tms/1.0

    This will be matched by the second regular expression in our tms application's
    urls.py module::

        ^(?P<version>[0-9.]+)$

    This regular expression will extract the 1.0 portion of the URL and assign it to a
    parameter named version. This parameter is then passed on to the view function
    associated with this URL pattern, as follows::

        tileMap(version="1.0")

    In this way, each of our URL patterns maps an incoming RESTful URL to the
    appropriate view function within our tms application. The included comments
    give examples of how the regular expressions will map to the view functions.

    Let's now set up these view functions. Edit the views.py module inside the tms
    directory, and add the following to this module::

        from django.http import HttpResponse

        def root(request):
            return HttpResponse("Tile Map Server")
        
        def service(request, version):
            return HttpResponse("Tile Map Service")

        def tileMap(request, version, shapefile_id):
            return HttpResponse("Tile Map")

        def tile(request, version, shapefile_id, zoom, x, y):
            return HttpResponse("Tile")

    Obviously these are only placeholder view functions, but they give us the basic
    structure for our Tile Map Server.

    To test that this works, launch the ShapeEditor server by running the
    python manage.py runserver command, and point your web browser to
    http://127.0.0.1:8000/tms. You should see the text you entered into your
    placeholder root() view function.

    Let's make that top-level view function do something useful. Go back to the tms
    application's views.py module, and change the root() function to look as follows::

        def root(request):
            try:
                baseURL = request.build_absolute_uri()
                xml = []
                xml.append('<?xml version="1.0" encoding="utf-8" ?>')
                xml.append('<Services>')
                xml.append(' <TileMapService ' +
                           'title="ShapeEditor Tile Map Service" ' +
                           'version="1.0" href="' + baseURL + '/1.0"/>')
                xml.append('</Services>')
                return HttpResponse("\n".join(xml), mimetype="text/xml")
            except:

                traceback.print_exc()
                return HttpResponse("Error")

    You'll also need to add the following import statement to the top of the module::
    
        import traceback

    This view function returns an XML-format response describing the one-and-only Tile
    Map Service supported by our TMS server. This Tile Map Service is identified by a
    version number, 1.0 (Tile Map Services are typically identified by version number).
    If you now go to http://127.0.0.1:8000/tms, you'll see the TMS response
    displayed in your web browser:

    .. image:: ./img/445-0.png
       :align: center

    As you can see, this provides a list of the Tile Map Services which this TMS server provides. OpenLayers will use this to access our Tile Map Service.

    .. note::

        Error handling

        Notice that we've wrapped our TMS view function in a try...
        except statement, and used the traceback standard library
        module to print out the exception if anything goes wrong.
        We're doing this because our code will be called directly by
        OpenLayers using AJAX; Django helpfully handles exceptions
        and returns an HTML error page to the caller, but in this case
        OpenLayers won't display that page if there is an error in your
        code. Instead, all you'll see are broken image icons instead of a
        map, and the error itself will remain a mystery.
        By wrapping our Python code in a try...except statement,
        we can catch any exceptions in our Python code and print them
        out. This will cause the error to appear in Django's web server
        log, so we can see what went wrong. This is a useful technique
        to use whenever you write AJAX request handlers in Python.

    We're now ready to implement the Tile Map Service itself. Edit the view.py module
    again, and change the service() function to look like this::

        def service(request, version):
            try:
                if version != "1.0":
                    raise Http404

                baseURL = request.build_absolute_uri()
                xml = []
                xml.append('<?xml version="1.0" encoding="utf-8" ?>')
                xml.append('<TileMapService version="1.0" services="' +
                            baseURL + '">')
                xml.append(' <Title>ShapeEditor Tile Map Service' +
                           '</Title>')
                xml.append(' <Abstract></Abstract>')
                xml.append(' <TileMaps>')
                for shapefile in Shapefile.objects.all():
                    id = str(shapefile.id)
                    xml.append('    <TileMap title="' +
                                shapefile.filename + '"')
                    xml.append('        srs="EPSG:4326"')
                    xml.append('        href="'+baseURL+'/'+id+'"/>')
                xml.append(' </TileMaps>')
                xml.append('</TileMapService>')
                return HttpResponse("\n".join(xml), mimetype="text/xml")

            except:
                traceback.print_exc()
                return HttpResponse("Error")
    
    You'll also need to add the following import statements to the top of the module::

        from django.http import Http404
        from geodjango.shapeEditor.models import Shapefile

    Notice that this function raises an Http404 exception if the version number is wrong.
    This exception tells Django to return a HTTP 404 error, which is the standard error
    response when an incorrect URL has been used.

    Assuming the version number is correct, we iterate over the various Shapefile
    objects in the database, listing each uploaded shapefile as a Tile Map.

    If you save this file and enter http://127.0.0.1:8000/tms/1.0 into your web
    browser, you should see a list of the available tile maps, in XML format:

    .. image:: ./img/447-0.png
       :align: center

    We next need to implement the *tileMap()* function to display the various Tile Sets
    available for a given Tile Map. Before we can do this, though, we're going to have to
    learn a bit about the notion of zoom levels.

    As we have seen, a slippy map lets the user zoom in and out to view the map's
    contents. This zooming is done by controlling the map's zoom level. Typically,
    a zoom level is specified as a simple number: zoom level zero is when the map
    is fully zoomed out, zoom level 1 is when the map is zoomed in once, and so on.

    Let's start by considering the map when it is zoomed out completely (in other words,
    zoom level 0). In this case, we want the entire Earth's surface to be covered by just
    two map tiles:

    .. image:: ./img/448-0.png
       :align: center

    Each map tile at this zoom level would cover 180 degrees of latitude and longitude.
    If each tile was 256 pixels square, this would mean that each pixel would cover 180
    / 256 = 0.703125 map units, where in this case a "map unit" is a degree of latitude or
    longitude. This number is going to be very important when it comes to calculating
    the Tile Maps.

    Now, whenever we zoom in (for example by going from zoom level 0 to zoom level
    1), the width and height of the visible area is halved. For example, at zoom level 1
    the Earth's surface would be displayed as the following series of eight tiles:

    .. image:: ./img/448-1.png
       :align: center

    Following this pattern, we can calculate the number of map units covered by a single
    pixel on the map, for a given zoom level, using the following formula:

    .. image:: ./img/449-0.png
       :align: center

    Since we'll be using this formula in our TMS server, let's go ahead and add the
    following code to the end of our tms.py module::

        def _unitsPerPixel(zoomLevel):
            return 0.703125 / math.pow(2, zoomLevel)

    .. note::

        Notice that we start the function name with an underscore; this is a standard Python convention for naming "private" functions within a module.

    You'll also need to add an import math statement to the top of the file.

    Next, we need to add some constants to the top of the module to define the size of
    each map tile, and how many zoom levels we support::
    
        MAX_ZOOM_LEVEL = 10
        TILE_WIDTH     = 256
        TILE_HEIGHT    = 256

    With all this, we're finally ready to implement the tileMap() function to return
    information about the available Tile Sets for a given shapefile's Tile Map. Edit this
    function to look as follows::

        def tileMap(request, version, shapefile_id):
            if version != "1.0":
                raise Http404

            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                raise Http404
            
            try:
                baseURL = request.build_absolute_uri()
                xml = []
                xml.append('<?xml version="1.0" encoding="utf-8" ?>')
                xml.append('<TileMap version="1.0" ' +
                           'tilemapservice="' + baseURL + '">')
                xml.append(' <Title>' + shapefile.filename + '</Title>')
                xml.append(' <Abstract></Abstract>')
                xml.append(' <SRS>EPSG:4326</SRS>')
                xml.append(' <BoundingBox minx="-180" miny="-90" ' +
                           'maxx="180" maxy="90"/>')
                xml.append(' <Origin x="-180" y="-90"/>')
                xml.append(' <TileFormat width="' + str(TILE_WIDTH) +
                           '" height="' + str(TILE_HEIGHT) + '" ' +
                           'mime-type="image/png" extension="png"/>')
                xml.append(' <TileSets profile="global-geodetic">')
                for zoomLevel in range(0, MAX_ZOOM_LEVEL+1):
                    unitsPerPixel = _unitsPerPixel(zoomLevel)
                    xml.append('    <TileSet href="' +
                                baseURL + '/' + str(zoomLevel) +
                                '" units-per-pixel="'+str(unitsPerPixel) +
                                '" order="' + str(zoomLevel) + '"/>')
                xml.append(' </TileSets>')
                xml.append('</TileMap>')
                return HttpResponse("\n".join(xml), mimetype="text/xml")
            except:
                traceback.print_exc()
                return HttpResponse("Error")

    As you can see, we start with some basic error checking on the version and shapefile
    ID, and then iterate through the available zoom levels to provide information about
    the available Tile Sets.

    If you save your changes and enter http://127.0.0.1:8000/tms/1.0/2 into your
    web browser, you should see the following information about the Tile Map for the
    shapefile object with record ID 2:

    .. image:: ./img/451-0.png
       :align: center

    Notice that we provide a total of eleven zoom levels, from zero to ten, with an
    appropriately-calculated units-per-pixel value for each zoom level.

    We have now implemented three of the four view functions required to implement
    our own Tile Map Server. For the final function, tile(), we are going to write
    our own tile renderer. The tile() function accepts a Tile Map Service version,
    a shapefile ID, a zoom level, and the X and Y coordinates for the desired tile::
    
        def tile(request, version, shapefile_id, zoom, x, y):
        ...
    
    This function needs to generate the appropriate map tile and return the rendered
    image back to the caller. Before we implement this function, let's take a step back
    and think about what the map rendering should look like.

    We want the map to include the outline of the various features within the given
    shapefile. However, by themselves these features won't look very meaningful:

    .. image:: ./img/452-0.png
       :align: center

    It isn't until these features are shown in context, by displaying a **base map** behind the
    features, that we can see what they are supposed to represent:

    .. image:: ./img/452-1.png
       :align: center

    Because of this, we're going to have to display a base map on which the features
    themselves are drawn. Let's build that base map, and then we can use this, along
    with the shapefile's features, to render the map tiles.


设置基础地图
~~~~~~~~~~~~~
Setting up the base map

.. tab:: 中文

    For our base map, we're going to use the World Borders Dataset we've used several times throughout this book. While this dataset doesn't look great when zoomed right in, it works well as a base map on which we can draw the shapefile's features.

    We'll start by creating a database model to hold the base map's data. Because the
    base map will be specific to our Tile Map Server application, we want to add a
    database table specific to this application. To do this, edit the models.py module
    inside the tms application directory, and change this file to look like the following::

        from django.contrib.gis.db import models
        class BaseMap(models.Model):
            name     = models.CharField(max_length=50)
            geometry = models.MultiPolygonField(srid=4326)

            objects = models.GeoManager()
            
            def __unicode__(self):
                return self.name

    .. note::

        Don't forget to change the import statement at the top of the file.

    As you can see, we're storing the country names as well as their geometries, which
    happen to be MultiPolygons. Now from the command line, cd into your project
    directory and type::

        % python manage.py syncdb

    This will create the database table used by the BaseMap object.

    .. note::

        Remember that there's a bug in Django 1.4 that prevents the
        geospatial fields from being created automatically. To fix this,
        run the Postgresql command-line client::

            $ psql shapeeditor

        You can then manually add the missing geometry field and its
        associated spatial index by typing in the following commands:

        .. code-block:: sql

            ALTER TABLE tms_basemap ADD COLUMN geometry geometry(MultiPolygon, 4326);
            CREATE INDEX tms_basemap_geometry_id ON tms_basemap USING GIST(geometry);

    Now that we have somewhere to store the base map, let's import the data. Place
    a copy of the World Borders Dataset shapefile somewhere convenient, open up a
    command line window, and cd into your shapeEditor project directory. Then type::

        % python manage.py shell
    
    This runs a Python interactive shell with your project's settings and paths installed.
    Now create the following variable, replacing the text with the absolute path to the
    World Borders Dataset's shapefile::

        >>> shapefile = "/path/to/TM_WORLD_BORDERS-0.3.shp"
        Then type the following:
        >>> from django.contrib.gis.utils import LayerMapping
        >>> from shapeEditor.tms.models import BaseMap
        >>> mapping = LayerMapping(BaseMap, shapefile, {'name' : "NAME",
        'geometry' : "MULTIPOLYGON"}, transform=False, encoding="iso-8859-1")
        >>> mapping.save(strict=True, verbose=True)
    
    We're using GeoDjango's LayerMapping module to import the data from this
    shapefile into our database. The various countries will be displayed as they are
    imported, which will take a few seconds.
    
    Once this has been done, you can check the imported data by typing commands
    into the interactive shell, for example::

        >>> print BaseMap.objects.count()
        246
        >>> print BaseMap.objects.all()
        [<BaseMap: Antigua and Barbuda>, <BaseMap: Algeria>, <BaseMap:
        Azerbaijan>, <BaseMap: Albania>, <BaseMap: Armenia>, <BaseMap: Angola>,
        <BaseMap: American Samoa>, <BaseMap: Argentina>, <BaseMap: Australia>,
        <BaseMap: Bahrain>, <BaseMap: Barbados>, <BaseMap: Bermuda>, <BaseMap:
        Bahamas>, <BaseMap: Bangladesh>, <BaseMap: Belize>, <BaseMap: Bosnia and
        Herzegovina>, <BaseMap: Bolivia>, <BaseMap: Burma>, <BaseMap: Benin>,
        <BaseMap: Solomon Islands>, '...(remaining elements truncated)...']
    
    Feel free to play some more if you want; the Django tutorial includes several examples of exploring your data objects using the interactive shell.

    Because this base map is going to be part of the ShapeEditor project itself (the
    application won't run without it), it would be good if Django could treat that
    data as part of the project's source code. That way, if we ever had to rebuild the
    database from scratch, the base map would be reinstalled automatically.

    Django allows you to do this by creating a **fixture**. A fixture is a set of data that can
    be loaded into the database on demand, either manually, or automatically when the
    database is initialized. We'll save our base map data into a fixture so that Django
    can reload that data as required.

    Create a directory named fixtures within the tms application directory. Then, in a
    terminal window, cd into the shapeEditor project directory and type::

        % python manage.py dumpdata tms > shapeEditor/tms/fixtures/initial_data.json

    This will create a fixture named initial_data.json for the tms application. As the
    name suggests, the contents of this fixture will be loaded automatically if Django
    ever has to re-initialize the database.

    Now that we have a base map, let's use it to implement our tile rendering code.

.. tab:: 英文

    For our base map, we're going to use the World Borders Dataset we've used several times throughout this book. While this dataset doesn't look great when zoomed right in, it works well as a base map on which we can draw the shapefile's features.

    We'll start by creating a database model to hold the base map's data. Because the
    base map will be specific to our Tile Map Server application, we want to add a
    database table specific to this application. To do this, edit the models.py module
    inside the tms application directory, and change this file to look like the following::

        from django.contrib.gis.db import models
        class BaseMap(models.Model):
            name     = models.CharField(max_length=50)
            geometry = models.MultiPolygonField(srid=4326)

            objects = models.GeoManager()
            
            def __unicode__(self):
                return self.name

    .. note::

        Don't forget to change the import statement at the top of the file.

    As you can see, we're storing the country names as well as their geometries, which
    happen to be MultiPolygons. Now from the command line, cd into your project
    directory and type::

        % python manage.py syncdb

    This will create the database table used by the BaseMap object.

    .. note::

        Remember that there's a bug in Django 1.4 that prevents the
        geospatial fields from being created automatically. To fix this,
        run the Postgresql command-line client::

            $ psql shapeeditor

        You can then manually add the missing geometry field and its
        associated spatial index by typing in the following commands:

        .. code-block:: sql

            ALTER TABLE tms_basemap ADD COLUMN geometry geometry(MultiPolygon, 4326);
            CREATE INDEX tms_basemap_geometry_id ON tms_basemap USING GIST(geometry);

    Now that we have somewhere to store the base map, let's import the data. Place
    a copy of the World Borders Dataset shapefile somewhere convenient, open up a
    command line window, and cd into your shapeEditor project directory. Then type::

        % python manage.py shell
    
    This runs a Python interactive shell with your project's settings and paths installed.
    Now create the following variable, replacing the text with the absolute path to the
    World Borders Dataset's shapefile::

        >>> shapefile = "/path/to/TM_WORLD_BORDERS-0.3.shp"
        Then type the following:
        >>> from django.contrib.gis.utils import LayerMapping
        >>> from shapeEditor.tms.models import BaseMap
        >>> mapping = LayerMapping(BaseMap, shapefile, {'name' : "NAME",
        'geometry' : "MULTIPOLYGON"}, transform=False, encoding="iso-8859-1")
        >>> mapping.save(strict=True, verbose=True)
    
    We're using GeoDjango's LayerMapping module to import the data from this
    shapefile into our database. The various countries will be displayed as they are
    imported, which will take a few seconds.
    
    Once this has been done, you can check the imported data by typing commands
    into the interactive shell, for example::

        >>> print BaseMap.objects.count()
        246
        >>> print BaseMap.objects.all()
        [<BaseMap: Antigua and Barbuda>, <BaseMap: Algeria>, <BaseMap:
        Azerbaijan>, <BaseMap: Albania>, <BaseMap: Armenia>, <BaseMap: Angola>,
        <BaseMap: American Samoa>, <BaseMap: Argentina>, <BaseMap: Australia>,
        <BaseMap: Bahrain>, <BaseMap: Barbados>, <BaseMap: Bermuda>, <BaseMap:
        Bahamas>, <BaseMap: Bangladesh>, <BaseMap: Belize>, <BaseMap: Bosnia and
        Herzegovina>, <BaseMap: Bolivia>, <BaseMap: Burma>, <BaseMap: Benin>,
        <BaseMap: Solomon Islands>, '...(remaining elements truncated)...']
    
    Feel free to play some more if you want; the Django tutorial includes several examples of exploring your data objects using the interactive shell.

    Because this base map is going to be part of the ShapeEditor project itself (the
    application won't run without it), it would be good if Django could treat that
    data as part of the project's source code. That way, if we ever had to rebuild the
    database from scratch, the base map would be reinstalled automatically.

    Django allows you to do this by creating a **fixture**. A fixture is a set of data that can
    be loaded into the database on demand, either manually, or automatically when the
    database is initialized. We'll save our base map data into a fixture so that Django
    can reload that data as required.

    Create a directory named fixtures within the tms application directory. Then, in a
    terminal window, cd into the shapeEditor project directory and type::

        % python manage.py dumpdata tms > shapeEditor/tms/fixtures/initial_data.json

    This will create a fixture named initial_data.json for the tms application. As the
    name suggests, the contents of this fixture will be loaded automatically if Django
    ever has to re-initialize the database.
    
    Now that we have a base map, let's use it to implement our tile rendering code.


Tile 渲染
~~~~~~~~~~~~~
Tile rendering

.. tab:: 中文

    Using our knowledge of Mapnik, we're going to implement the TMS server's tile()
    function. Our generated map will consist of two layers: a **base layer** showing the base
    map, and a **feature layer** showing the features in the imported shapefile. Since all our
    data is stored in a PostGIS database, we'll be using a mapnik.PostGIS datasource for
    both layers.

    Our tile() function will involve five steps:
    
    1. Parse the query parameters.
    2. Set up the map.
    3. Define the base layer.
    4. Define the feature layer.
    5. Render the map.
    
    Let's work through each of these in turn.

.. tab:: 英文

    Using our knowledge of Mapnik, we're going to implement the TMS server's tile()
    function. Our generated map will consist of two layers: a **base layer** showing the base
    map, and a **feature layer** showing the features in the imported shapefile. Since all our
    data is stored in a PostGIS database, we'll be using a mapnik.PostGIS datasource for
    both layers.

    Our tile() function will involve five steps:
    
    1. Parse the query parameters.
    2. Set up the map.
    3. Define the base layer.
    4. Define the feature layer.
    5. Render the map.
    
    Let's work through each of these in turn.

解析查询参数
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Parsing the query parameters

.. tab:: 中文

    Edit the tms application's views.py module, and delete the dummy code we had
    in the tile() function. We'll add our parsing code one step at a time, starting with
    some basic error-checking code to ensure the version number is correct and that the
    shapefile exists, and once again wrapping our code in a try...except statement to
    catch typos and other errors::

        try:
            if version != "1.0":
                raise Http404
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                raise Http404

    We now need to convert the query parameters (which Django passes to us as strings)
    into integers so that we can work with them::

        zoom = int(zoom)
        x    = int(x)
        y    = int(y)

    We can now check that the zoom level is correct::

        if zoom < 0 or zoom > MAX_ZOOM_LEVEL:
            raise Http404

    Our next step is to convert the supplied x and y parameters into the minimum and
    maximum latitude and longitude values covered by the tile. This requires us to use
    the _unitsPerPixel() function we defined earlier to calculate the amount of the
    Earth's surface covered by the tile for the current zoom level::

        xExtent = _unitsPerPixel(zoom) * TILE_WIDTH
        yExtent = _unitsPerPixel(zoom) * TILE_HEIGHT

        minLong = x * xExtent - 180.0
        minLat = y * yExtent - 90.0
        maxLong = minLong + xExtent
        maxLat = minLat + yExtent

    Finally, we can add some rudimentary error checking to ensure that the tile's coordinates are valid::

        if (minLong < -180 or maxLong > 180 or
            minLat < -90 or maxLat > 90):
            raise Http404

.. tab:: 英文

    Edit the tms application's views.py module, and delete the dummy code we had
    in the tile() function. We'll add our parsing code one step at a time, starting with
    some basic error-checking code to ensure the version number is correct and that the
    shapefile exists, and once again wrapping our code in a try...except statement to
    catch typos and other errors::

        try:
            if version != "1.0":
                raise Http404
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                raise Http404

    We now need to convert the query parameters (which Django passes to us as strings)
    into integers so that we can work with them::

        zoom = int(zoom)
        x    = int(x)
        y    = int(y)

    We can now check that the zoom level is correct::

        if zoom < 0 or zoom > MAX_ZOOM_LEVEL:
            raise Http404

    Our next step is to convert the supplied x and y parameters into the minimum and
    maximum latitude and longitude values covered by the tile. This requires us to use
    the _unitsPerPixel() function we defined earlier to calculate the amount of the
    Earth's surface covered by the tile for the current zoom level::

        xExtent = _unitsPerPixel(zoom) * TILE_WIDTH
        yExtent = _unitsPerPixel(zoom) * TILE_HEIGHT

        minLong = x * xExtent - 180.0
        minLat = y * yExtent - 90.0
        maxLong = minLong + xExtent
        maxLat = minLat + yExtent

    Finally, we can add some rudimentary error checking to ensure that the tile's coordinates are valid::

        if (minLong < -180 or maxLong > 180 or
            minLat < -90 or maxLat > 90):
            raise Http404

设置地图
^^^^^^^^^^^^^^^^^^^^^^^
Setting up the map

.. tab:: 中文

    We're now ready to create the mapnik.Map object to represent the map. This is trivial::

        map = mapnik.Map(TILE_WIDTH, TILE_HEIGHT,
                        "+proj=longlat +datum=WGS84")
        map.background = mapnik.Color("#7391ad")

.. tab:: 英文

    We're now ready to create the mapnik.Map object to represent the map. This is trivial::

        map = mapnik.Map(TILE_WIDTH, TILE_HEIGHT,
                        "+proj=longlat +datum=WGS84")
        map.background = mapnik.Color("#7391ad")


定义基础层
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Defining the base layer

.. tab:: 中文

    We now want to define the layer which draws our base map. To do this, we have to
    set up a mapnik.PostGIS datasource for the layer::

        dbSettings = settings.DATABASES['default']
        datasource = \
            mapnik.PostGIS(user=dbSettings['USER'],
                           password=dbSettings['PASSWORD'],
                           dbname=dbSettings['NAME'],
                           table='tms_basemap',
                           srid=4326,
                           geometry_field="geometry",
                           geometry_table='tms_basemap')

    As you can see, we get the name of the database, the username, and the password
    from our project's settings module. We then create a PostGIS datasource using
    these settings. With this data source, we can now create the base layer itself::

        baseLayer = mapnik.Layer("baseLayer")
        baseLayer.datasource = datasource
        baseLayer.styles.append("baseLayerStyle")

    We now need to set up the layer's style. In this case, we'll use a single rule with
    two symbolizers: a PolygonSymbolizer which draws the interior of the base map's
    polygons, and a LineSymbolizer to draw the polygon outlines::

        rule = mapnik.Rule()

        rule.symbols.append(
            mapnik.PolygonSymbolizer(mapnik.Color("#b5d19c")))
        
        rule.symbols.append(
            mapnik.LineSymbolizer(mapnik.Color("#404040"), 0.2))

        style = mapnik.Style()
        style.rules.append(rule)

    Finally, we can add the base layer and its style to the map::

        map.append_style("baseLayerStyle", style)
        map.layers.append(baseLayer)

.. tab:: 英文

    We now want to define the layer which draws our base map. To do this, we have to
    set up a mapnik.PostGIS datasource for the layer::

        dbSettings = settings.DATABASES['default']
        datasource = \
            mapnik.PostGIS(user=dbSettings['USER'],
                           password=dbSettings['PASSWORD'],
                           dbname=dbSettings['NAME'],
                           table='tms_basemap',
                           srid=4326,
                           geometry_field="geometry",
                           geometry_table='tms_basemap')

    As you can see, we get the name of the database, the username, and the password
    from our project's settings module. We then create a PostGIS datasource using
    these settings. With this data source, we can now create the base layer itself::

        baseLayer = mapnik.Layer("baseLayer")
        baseLayer.datasource = datasource
        baseLayer.styles.append("baseLayerStyle")

    We now need to set up the layer's style. In this case, we'll use a single rule with
    two symbolizers: a PolygonSymbolizer which draws the interior of the base map's
    polygons, and a LineSymbolizer to draw the polygon outlines::

        rule = mapnik.Rule()

        rule.symbols.append(
            mapnik.PolygonSymbolizer(mapnik.Color("#b5d19c")))
        
        rule.symbols.append(
            mapnik.LineSymbolizer(mapnik.Color("#404040"), 0.2))

        style = mapnik.Style()
        style.rules.append(rule)

    Finally, we can add the base layer and its style to the map::

        map.append_style("baseLayerStyle", style)
        map.layers.append(baseLayer)

定义要素层
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Defining the feature layer

.. tab:: 中文

    Our next task is to add another layer to draw the shapefile's features onto the map.
    Once again, we'll set up a mapnik.PostGIS datasource for the new layer::

        geometry_field = utils.calc_geometry_field(shapefile.geom_type)

        query = '(select ' + geometry_field \
              + ' from "shared_feature" where' \
              + ' shapefile_id=' + str(shapefile.id) + ') as geom'

        datasource = \
            mapnik.PostGIS(user=dbSettings['USER'],
                           password=dbSettings['PASSWORD'],
                           dbname=dbSettings['NAME'],
                           table=query,
                           srid=4326,
                           geometry_field=geometryField,
                           geometry_table='shared_feature')

    In this case, we are calling utils.calc_geometry_field() to see which field in the shared_feature table contains the geometry we're going to display.

    We're now ready to create the new layer itself::

        featureLayer = mapnik.Layer("featureLayer")
        featureLayer.datasource = datasource
        featureLayer.styles.append("featureLayerStyle")

    Next, we want to define the styles used by the feature layer. As before, we'll have just
    a single rule, but in this case we'll use different symbolizers depending on the type of
    feature we are displaying::

        rule = mapnik.Rule()

        if shapefile.geom_type in ["Point", "MultiPoint"]:
            rule.symbols.append(mapnik.PointSymbolizer())
        elif shapefile.geom_type in ["LineString", "MultiLineString"]:
            rule.symbols.append(
                mapnik.LineSymbolizer(mapnik.Color("#000000"), 0.5))
        elif shapefile.geom_type in ["Polygon", "MultiPolygon"]:
            rule.symbols.append(
                mapnik.PolygonSymbolizer(mapnik.Color("#f7edee")))
            rule.symbols.append(
                mapnik.LineSymbolizer(mapnik.Color("#000000"), 0.5))

        style = mapnik.Style()
        style.rules.append(rule)

    Finally, we can add our new feature layer to the map::
    
        map.append_style("featureLayerStyle", style)
        map.layers.append(featureLayer)

.. tab:: 英文

    Our next task is to add another layer to draw the shapefile's features onto the map.
    Once again, we'll set up a mapnik.PostGIS datasource for the new layer::

        geometry_field = utils.calc_geometry_field(shapefile.geom_type)

        query = '(select ' + geometry_field \
              + ' from "shared_feature" where' \
              + ' shapefile_id=' + str(shapefile.id) + ') as geom'

        datasource = \
            mapnik.PostGIS(user=dbSettings['USER'],
                           password=dbSettings['PASSWORD'],
                           dbname=dbSettings['NAME'],
                           table=query,
                           srid=4326,
                           geometry_field=geometryField,
                           geometry_table='shared_feature')

    In this case, we are calling utils.calc_geometry_field() to see which field in the shared_feature table contains the geometry we're going to display.

    We're now ready to create the new layer itself::

        featureLayer = mapnik.Layer("featureLayer")
        featureLayer.datasource = datasource
        featureLayer.styles.append("featureLayerStyle")

    Next, we want to define the styles used by the feature layer. As before, we'll have just
    a single rule, but in this case we'll use different symbolizers depending on the type of
    feature we are displaying::

        rule = mapnik.Rule()

        if shapefile.geom_type in ["Point", "MultiPoint"]:
            rule.symbols.append(mapnik.PointSymbolizer())
        elif shapefile.geom_type in ["LineString", "MultiLineString"]:
            rule.symbols.append(
                mapnik.LineSymbolizer(mapnik.Color("#000000"), 0.5))
        elif shapefile.geom_type in ["Polygon", "MultiPolygon"]:
            rule.symbols.append(
                mapnik.PolygonSymbolizer(mapnik.Color("#f7edee")))
            rule.symbols.append(
                mapnik.LineSymbolizer(mapnik.Color("#000000"), 0.5))

        style = mapnik.Style()
        style.rules.append(rule)

    Finally, we can add our new feature layer to the map::
    
        map.append_style("featureLayerStyle", style)
        map.layers.append(featureLayer)


渲染地图图块
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Rendering the map tile

.. tab:: 中文

    We looked at using Mapnik to render map images in Bonus Chapter, Web Frameworks
    for Python Geospatial Development. The basic process of rendering a map tile is the
    same, except that we won't be storing the results into an image file on disk. Instead,
    we'll create a mapnik.Image object, convert it into raw image data in PNG format,
    and return that data back to the caller using an HttpResponse object::

        map.zoom_to_box(mapnik.Box2d(minLong, minLat,
                                     maxLong, maxLat))
        image = mapnik.Image(TILE_WIDTH, TILE_HEIGHT)
        mapnik.render(map, image)
        imageData = image.tostring('png')

        return HttpResponse(imageData, mimetype="image/png")

    All that's left now is to add our error-catching code to the end of the function::

        except:
            traceback.print_exc()
            return HttpResponse("Error")

    That completes the implementation of our Tile Map Server's tile() function. Let's tidy up and do some testing.

.. tab:: 英文

    We looked at using Mapnik to render map images in Bonus Chapter, Web Frameworks
    for Python Geospatial Development. The basic process of rendering a map tile is the
    same, except that we won't be storing the results into an image file on disk. Instead,
    we'll create a mapnik.Image object, convert it into raw image data in PNG format,
    and return that data back to the caller using an HttpResponse object::

        map.zoom_to_box(mapnik.Box2d(minLong, minLat,
                                     maxLong, maxLat))
        image = mapnik.Image(TILE_WIDTH, TILE_HEIGHT)
        mapnik.render(map, image)
        imageData = image.tostring('png')

        return HttpResponse(imageData, mimetype="image/png")

    All that's left now is to add our error-catching code to the end of the function::

        except:
            traceback.print_exc()
            return HttpResponse("Error")

    That completes the implementation of our Tile Map Server's tile() function. Let's tidy up and do some testing.


完成图块地图服务器
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Completing the Tile Map Server

.. tab:: 中文

    Because we've referred to some new modules in our views.py module, we'll have to
    add some extra import statements to the top of the file::

        from django.conf import settings
        import mapnik
        import utils

    In theory, our Tile Map Server should now be ready to go. Let's test it out. If you
    don't currently have the Django web server running, cd into the shapeEditor project
    directory and type::

        % python manage.py runserver

    Start up your web browser and enter the following URL into your browser's
    address bar::
        
        http://127.0.0.1:8000/tms/1.0/2/0/0/0.png
    
    All going well, you should see a 256 x 256 pixel map tile appear in your web browser:

    .. image:: ./img/460-0.png
       :align: center

    .. note::
        
        **Problems?**

        If an error occurs, there are two likely causes: you might have made
        a mistake typing in the code, or you might have the record ID of the
        shapefile wrong. Check the web server log in the terminal window
        you used to run the python manage.py runserver command;

        when a Python exception occurs, the traceback will be printed out
        in this window. This will tell you if you have a syntax error, some
        other error, or if an Http404 exception was raised.

        If you do get an Http404 exception, it's most likely because you're
        using the wrong record ID for the shapefile. The URL is structured
        like this:

        http://path/to/tms/<version>/<shapefile_id>/<zoom>/<x>/<y>.png

        If you've been working through this chapter in order, the record ID
        of the World Borders Dataset shapefile you imported earlier should
        be 2, but if you've imported other shapefiles in the meantime,
        or created more shapefile records while playing with the admin
        interface, you may need to use a different record ID. To see what
        record ID a given shapefile has, go to http://127.0.0.1:8000/
        editor and click on the Edit hyperlink for the desired shapefile.
        You'll see a Page Not Found error, but the final part of the
        hyperlink will be the record ID of the shapefile. Replace the record
        ID in the previous URL with the correct ID, and the map tile should
        appear.

    Once you've reached the point of seeing the previous image in your web browser, you deserve a pat on the back: congratulations, you have just implemented your own working Tile Map Server!

.. tab:: 英文

    Because we've referred to some new modules in our views.py module, we'll have to
    add some extra import statements to the top of the file::

        from django.conf import settings
        import mapnik
        import utils

    In theory, our Tile Map Server should now be ready to go. Let's test it out. If you
    don't currently have the Django web server running, cd into the shapeEditor project
    directory and type::

        % python manage.py runserver

    Start up your web browser and enter the following URL into your browser's
    address bar::
        
        http://127.0.0.1:8000/tms/1.0/2/0/0/0.png
    
    All going well, you should see a 256 x 256 pixel map tile appear in your web browser:

    .. image:: ./img/460-0.png
       :align: center

    .. note::
        
        **Problems?**

        If an error occurs, there are two likely causes: you might have made
        a mistake typing in the code, or you might have the record ID of the
        shapefile wrong. Check the web server log in the terminal window
        you used to run the python manage.py runserver command;

        when a Python exception occurs, the traceback will be printed out
        in this window. This will tell you if you have a syntax error, some
        other error, or if an Http404 exception was raised.

        If you do get an Http404 exception, it's most likely because you're
        using the wrong record ID for the shapefile. The URL is structured
        like this:

        http://path/to/tms/<version>/<shapefile_id>/<zoom>/<x>/<y>.png

        If you've been working through this chapter in order, the record ID
        of the World Borders Dataset shapefile you imported earlier should
        be 2, but if you've imported other shapefiles in the meantime,
        or created more shapefile records while playing with the admin
        interface, you may need to use a different record ID. To see what
        record ID a given shapefile has, go to http://127.0.0.1:8000/
        editor and click on the Edit hyperlink for the desired shapefile.
        You'll see a Page Not Found error, but the final part of the
        hyperlink will be the record ID of the shapefile. Replace the record
        ID in the previous URL with the correct ID, and the map tile should
        appear.

    Once you've reached the point of seeing the previous image in your web browser, you deserve a pat on the back: congratulations, you have just implemented your own working Tile Map Server!



使用 OpenLayers 显示地图
--------------------------------------
Using OpenLayers to display the map

.. tab:: 中文

    Now that we have our TMS server up and running, we can use the OpenLayers
    library to display the rendered map tiles within a slippy map. This slippy map
    will be used within our edit shapefile view function to display all the shapefile's
    features, allowing the user to select a feature within the shapefile to edit.

    Let's implement this edit shapefile view. Edit the urls.py module within the
    editor application, and add the following highlighted entry to this file::

        urlpatterns = patterns('shapeEditor.editor.views',
                url(r'^$', 'list_shapefiles'),
                url(r'^import$', 'import_shapefile'),
                url(r'^export/(?P<shapefile_id>\d+)$', 'export_shapefile'),
                url(r'^edit/(?P<shapefile_id>\d+)$', 'edit_shapefile'),
        )

    This will pass any incoming URLs of the form /editor/edit/N to the
    edit_shapefile() view function.

    Let's implement that function. Edit the editor application's views.py module
    and add the following code::

        def edit_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            tms_url = "http://"+request.get_host()+"/tms/"

            return render(request, "select_feature.html",
                        {'shapefile' : shapefile,
                        'tms_url': tms_url})

    As you can see, we obtain the desired Shapefile object, calculate the URL used to
    access our TMS server, and pass both on to a template called select_feature.html.
    That template is where all the hard work will take place.

    Now we need to write the template. Start by creating a new file named select_feature.html in the editor application's templates directory, and enter the following into this file:

    .. code-block:: html

        <html>
            <head>
                <title>ShapeEditor</title>
                <style type="text/css">
                    div#map {
                        width: 600px;
                        height: 400px;
                        border: 1px solid #ccc;
                    }
                </style>
            </head>
            <body>
                <h1>Edit Shapefile</h1>
                <b>Please choose a feature to edit</b>
                <br/>
                <div id="map" class="map"></div>
                <br/>
                <div style="margin-left:20px">
                    <button type="button"
                        onClick='window.location="/editor";'>
                        Cancel
                    </button>
                </div>
            </body>
        </html>

    This is only the basic outline for this template, but it gives us something to work with. With the Django development server running (**python manage.py runserver** in a terminal window), go to http://127.0.0.1:8000/editor click on the **Edit** hyperlink for a shapefile. You should see the basic outline for the select feature page::

    .. image:: ./img/463-0.png
       :align: center

    Notice that we created a <div> element to hold the OpenLayers map, and we use
    a CSS stylesheet to give the map a fixed size and border. The map itself isn't being
    displayed yet, because we haven't written the JavaScript code needed to launch
    OpenLayers. Let's do that now.

    Add the following <script> tags to the <head> section of your template::

        <script src="http://openlayers.org/api/OpenLayers.js">
        </script>
        <script type="text/javascript">
            function init() {}
        </script>

    Also, change the <body> tag definition to look like this::
    
        <body onload="init()">

    Notice that there are two <script> tags: the first loads the OpenLayers.js library
    from the http://openlayers.org website, while the second will hold the JavaScript
    code that we'll write to create the map. We've also defined a JavaScript function
    called init() which will be called when the page is loaded.

    Let's implement that initialization function. Replace the line which says function
    init() {} with the following:

    .. code-block:: js

        function init() {
            map = new OpenLayers.Map('map',
                                    {maxResolution: 0.703125,
                                    numZoomLevels: 11});
            layer = new OpenLayers.Layer.TMS('TMS',
                                    "{{ tms_url }}",
                                    {serviceVersion: "1.0",
                                    layername: "{{ shapefile.id }}",
                                    type: 'png'});
            map.addLayer(layer);
            map.zoomToMaxExtent();
        }

    Even if you haven't used JavaScript before, this code should be quite straightforward:
    the first instruction creates an OpenLayers.Map object representing the slippy map.
    We then create an OpenLayers.Layer.TMS object to represent a map layer that takes
    data from a TMS server. Then we add the layer to the map, and zoom the map out as
    far as possible so that the user sees the entire world when the map is first displayed.

    Notice that the Map object accepts the ID of the <div> tag in which to place the map,
    along with a dictionary of options. The maxResolution option defines the maximum
    resolution to use for the map, and the numZoomLevels option tells OpenLayers how
    many zoom levels the map should support.

    For the Layer.TMS object, we pass in the URL used to access the Tile Map Server
    (which is a parameter passed to the template from our Python view), along with the
    version of the Tile Map Service to use and the name of the layer—which in our Tile
    Map Server is the record ID of the shapefile to display the features for.

    That's all we need to do to get a basic slippy map working with OpenLayers. Save
    your changes, start up the Django web server if it isn't already running, and point
    your web browser to http://127.0.0.1:8000/editor. Click on the Edit hyperlink
    for the shapefile you imported, and you should see the working slippy map:

    .. image:: ./img/465-0.png
       :align: center

    You can zoom in and out, pan around, and click to your heart's content. Of course,
    nothing actually works yet (apart from the Cancel button), but we have got a slippy
    map working with our Tile Map Server and the OpenLayers JavaScript widget.
    That's quite an achievement!

    .. note::

        **What if it doesn't work?**

        If the map isn't being shown for some reason, there are several
        possible causes. First, check the Django web server log, as we
        are printing any Python exceptions there. If that doesn't reveal
        the problem, look at your web browser's error console window
        to see if there are any errors at the JavaScript level. Because we
        are now writing JavaScript code, error messages will appear
        within the web browser rather than in Django's server log. In
        Firefox, you can view JavaScript errors by selecting the **Error**
        **Console** item from the **Tools** menu. Other browsers have
        similar windows for showing JavaScript errors.

        JavaScript debugging can be quite tricky, even for people
        experienced with developing web-based applications. If you do
        get stuck, you may find the following article helpful: http://
        www.webmonkey.com/2010/02/javascript_debugging_for_beginners

.. tab:: 英文

    Now that we have our TMS server up and running, we can use the OpenLayers
    library to display the rendered map tiles within a slippy map. This slippy map
    will be used within our edit shapefile view function to display all the shapefile's
    features, allowing the user to select a feature within the shapefile to edit.

    Let's implement this edit shapefile view. Edit the urls.py module within the
    editor application, and add the following highlighted entry to this file::

        urlpatterns = patterns('shapeEditor.editor.views',
                url(r'^$', 'list_shapefiles'),
                url(r'^import$', 'import_shapefile'),
                url(r'^export/(?P<shapefile_id>\d+)$', 'export_shapefile'),
                url(r'^edit/(?P<shapefile_id>\d+)$', 'edit_shapefile'),
        )

    This will pass any incoming URLs of the form /editor/edit/N to the
    edit_shapefile() view function.

    Let's implement that function. Edit the editor application's views.py module
    and add the following code::

        def edit_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            tms_url = "http://"+request.get_host()+"/tms/"

            return render(request, "select_feature.html",
                        {'shapefile' : shapefile,
                        'tms_url': tms_url})

    As you can see, we obtain the desired Shapefile object, calculate the URL used to
    access our TMS server, and pass both on to a template called select_feature.html.
    That template is where all the hard work will take place.

    Now we need to write the template. Start by creating a new file named select_feature.html in the editor application's templates directory, and enter the following into this file:

    .. code-block:: html

        <html>
            <head>
                <title>ShapeEditor</title>
                <style type="text/css">
                    div#map {
                        width: 600px;
                        height: 400px;
                        border: 1px solid #ccc;
                    }
                </style>
            </head>
            <body>
                <h1>Edit Shapefile</h1>
                <b>Please choose a feature to edit</b>
                <br/>
                <div id="map" class="map"></div>
                <br/>
                <div style="margin-left:20px">
                    <button type="button"
                        onClick='window.location="/editor";'>
                        Cancel
                    </button>
                </div>
            </body>
        </html>

    This is only the basic outline for this template, but it gives us something to work with. With the Django development server running (**python manage.py runserver** in a terminal window), go to http://127.0.0.1:8000/editor click on the **Edit** hyperlink for a shapefile. You should see the basic outline for the select feature page::

    .. image:: ./img/463-0.png
       :align: center

    Notice that we created a <div> element to hold the OpenLayers map, and we use
    a CSS stylesheet to give the map a fixed size and border. The map itself isn't being
    displayed yet, because we haven't written the JavaScript code needed to launch
    OpenLayers. Let's do that now.

    Add the following <script> tags to the <head> section of your template::

        <script src="http://openlayers.org/api/OpenLayers.js">
        </script>
        <script type="text/javascript">
            function init() {}
        </script>

    Also, change the <body> tag definition to look like this::
    
        <body onload="init()">

    Notice that there are two <script> tags: the first loads the OpenLayers.js library
    from the http://openlayers.org website, while the second will hold the JavaScript
    code that we'll write to create the map. We've also defined a JavaScript function
    called init() which will be called when the page is loaded.

    Let's implement that initialization function. Replace the line which says function
    init() {} with the following:

    .. code-block:: js

        function init() {
            map = new OpenLayers.Map('map',
                                    {maxResolution: 0.703125,
                                    numZoomLevels: 11});
            layer = new OpenLayers.Layer.TMS('TMS',
                                    "{{ tms_url }}",
                                    {serviceVersion: "1.0",
                                    layername: "{{ shapefile.id }}",
                                    type: 'png'});
            map.addLayer(layer);
            map.zoomToMaxExtent();
        }

    Even if you haven't used JavaScript before, this code should be quite straightforward:
    the first instruction creates an OpenLayers.Map object representing the slippy map.
    We then create an OpenLayers.Layer.TMS object to represent a map layer that takes
    data from a TMS server. Then we add the layer to the map, and zoom the map out as
    far as possible so that the user sees the entire world when the map is first displayed.

    Notice that the Map object accepts the ID of the <div> tag in which to place the map,
    along with a dictionary of options. The maxResolution option defines the maximum
    resolution to use for the map, and the numZoomLevels option tells OpenLayers how
    many zoom levels the map should support.

    For the Layer.TMS object, we pass in the URL used to access the Tile Map Server
    (which is a parameter passed to the template from our Python view), along with the
    version of the Tile Map Service to use and the name of the layer—which in our Tile
    Map Server is the record ID of the shapefile to display the features for.

    That's all we need to do to get a basic slippy map working with OpenLayers. Save
    your changes, start up the Django web server if it isn't already running, and point
    your web browser to http://127.0.0.1:8000/editor. Click on the Edit hyperlink
    for the shapefile you imported, and you should see the working slippy map:

    .. image:: ./img/465-0.png
       :align: center

    You can zoom in and out, pan around, and click to your heart's content. Of course,
    nothing actually works yet (apart from the Cancel button), but we have got a slippy
    map working with our Tile Map Server and the OpenLayers JavaScript widget.
    That's quite an achievement!

    .. note::

        **What if it doesn't work?**

        If the map isn't being shown for some reason, there are several
        possible causes. First, check the Django web server log, as we
        are printing any Python exceptions there. If that doesn't reveal
        the problem, look at your web browser's error console window
        to see if there are any errors at the JavaScript level. Because we
        are now writing JavaScript code, error messages will appear
        within the web browser rather than in Django's server log. In
        Firefox, you can view JavaScript errors by selecting the **Error**
        **Console** item from the **Tools** menu. Other browsers have
        similar windows for showing JavaScript errors.

        JavaScript debugging can be quite tricky, even for people
        experienced with developing web-based applications. If you do
        get stuck, you may find the following article helpful: http://
        www.webmonkey.com/2010/02/javascript_debugging_for_beginners


拦截鼠标点击
--------------------------------------
Intercepting mouse clicks

.. tab:: 中文

    When the user clicks on the map, we want to intercept that mouse click, identify
    the map coordinate that the user clicked on, and then ask the server to identify the
    clicked-on feature (if any). To intercept mouse clicks, we will need to create a custom
    OpenLayers.Control subclass. We'll follow the OpenLayers convention of adding
    the subclass to the OpenLayers namespace, by calling our new control OpenLayers.
    Control.Click. Once we've defined our new control, we can create an instance of
    the control and add it to the map so that the control can respond to mouse clicks.

    All of this has to be done in JavaScript. The code can be a bit confusing, so let's take
    this one step at a time. Edit your selectFeature.html file and add the following
    code to the <script> tag, immediately before your init() function:

    .. code-block:: js

        OpenLayers.Control.Click = OpenLayers.Class(
            OpenLayers.Control, {
                defaultHandlerOptions: {
                    'single': true,
                    'double': false,
                    'pixelTolerance' : 0,
                    'stopSingle': false,
                    'stopDouble': false
                },
                initialize: function(options) {
                    this.handlerOptions = OpenLayers.Util.extend(
                        {}, this.defaultHandlerOptions);
                    OpenLayers.Control.prototype.initialize.apply(
                        this, arguments);
                    this.handler = new OpenLayers.Handler.Click(
                        this, {'click' : this.onClick}, this.handlerOptions);
                },

                onClick: function(e) {
                        alert("click")
                }
            }
        );

    Don't worry too much about the details here—the initialize() function is
    a bit of black magic that creates a new OpenLayers.Control.Click instance
    and sets it up to run as an OpenLayers control. What is interesting to us is the
    defaultHandlerOptions dictionary, and the onClick() function.

    The defaultHandlerOptions dictionary tells OpenLayers how you want the
    click handler to respond to mouse clicks. In this case, we want to respond to single
    clicks, but not double clicks (as these are used to zoom further in to the map).

    The onClick() function is actually a JavaScript method for our OpenLayers.
    Control.Click class. This method will be called when the user clicks on the
    map—at the moment, all we're doing is displaying an alert box with the message
    Click, but that's enough to ensure that the click control is working.

    Now that we've defined our new click control, let's add it to the map. Add the
    following lines immediately before the closing } for the init() function:

    .. code-block:: js

        var click = new OpenLayers.Control.Click();
        map.addControl(click);
        click.activate();

    As you can see, we create a new instance of our OpenLayers.Control.Click class,
    add it to the map, and activate it.

    With all this code written, we can now reload the Select Feature web page and see what happens when the user clicks on a map:

    .. image:: ./img/468-0.png
       :align: center

    So far so good. Notice that our click handler only intercepts single clicks; if you
    double-click on the map, it will still zoom in.

    .. note::

        If your map isn't working, you may have made a mistake typing in the JavaScript code. Open your browser's JavaScript console or log window, and reload the page. An error message will appear in this window if there is a problem with your JavaScript code.

    Let's now implement the real onClick() function to respond to the user's mouse-
    click. When the user clicks on the map, we're going to send the clicked-on latitude
    and longitude value to the server using an AJAX request. The server will return
    the URL of the edit feature page for the clicked-on feature, or an empty string if no
    feature was clicked on. If a URL was returned, we'll then redirect the user's web
    browser to that URL.

    To make the AJAX call, we're going to use the OpenLayers.Request.GET function,
    passing in a callback function which will be called when a response is received back
    from the server. Let's start by writing the AJAX call.

    Replace our dummy onClick() function with the following:

    .. code-block:: js

        onClick: function(e) {
            var coord = map.getLonLatFromViewPortPx(e.xy);
            var request = OpenLayers.Request.GET({
                url: "{{ find_feature_url }}",
                params: {shapefile_id : {{ shapefile.id }},
                         latitude: coord.lat,
                         longitude: coord.lon},
                callback : this.handleResponse
            });
        }

    This function does two things: it obtains the map coordinate that corresponds to
    the clicked-on point (by calling the map.getLonLatFromViewPortPx() method),
    and then it creates an OpenLayers.Request.GET object to send the AJAX request
    to the server and call the handleResponse() callback function when the response
    is received.

    Notice that the OpenLayers.Request.GET() function accepts a set of query
    parameters (in the params entry), as well as a URL to send the request to (in the url
    entry) and a callback function to call when the response is received (in the callback
    entry). We're using a template parameter, {{ find_feature_url }}, to select
    the URL to send the request to. This will be provided by our edit_shapeFile()
    view function when the template is loaded. When we make the request, the query
    parameters will consist of the record ID of the shapefile and the clicked-on latitude
    and longitude values.

    While we're editing the select_feature.html template, let's go ahead and implement the callback function. Add the following function to the end of the OpenLayers.Control.Click class definition (immediately below the closing } for the onClick() function)::

    .. code-block:: js

        handleResponse: function(request) {
            if (request.status != 200) {
                alert("Server returned a "+request.status+" error");
                return;
            };
            if (request.responseText != "") {
                window.location.href = request.responseText;
            };
        }

    .. note::

        Make sure you add a comma after the onClick() function's closing parenthesis, or you'll get a JavaScript error. Just as with Python, you need to add commas to separate dictionary entries in JavaScript.

    Even if you're not familiar with JavaScript, this function should be easy to
    understand: if the response didn't have a status value of 200, an error message is
    displayed. Otherwise, we check that the response text is not blank, and if so we
    redirect the user's web browser to that URL.

    Now that we've implemented our callback function, let's go back to our view module
    and define the find_feature_url parameter which will get passed to the template
    we've created. Edit the view.py module to add the following highlighted lines to the
    edit_shapefile() function::

    .. code-block:: js

        def edit_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            tms_url = "http://"+request.get_host()+"/tms/"
            find_feature_url = "http://" + request.get_host() \
                            + "/editor/find_feature"

            return render(request, "select_feature.html",
                         {'shapefile': shapefile,
                          'find_feature_url' : find_feature_url,
                          'tms_url': tms_url})        

    The find_feature_url template parameter will contain the URL the click handler
    will send its AJAX request to. This URL will look like the following:

    http://127.0.0.1:8000/editor/find_feature
    
    Our onClick() function will add shapefile_id, latitude and longitude
    query parameters to this request, so the AJAX request sent to the server will
    look like the following::

    http://127.0.0.1:8000/editor/find_feature?shapefile_id=1&latitude=-38.1674&longitude=176.2344

    With our click handler up and running, we're now ready to start implementing the
    find_feature() view function to respond to these AJAX requests.

.. tab:: 英文

    When the user clicks on the map, we want to intercept that mouse click, identify
    the map coordinate that the user clicked on, and then ask the server to identify the
    clicked-on feature (if any). To intercept mouse clicks, we will need to create a custom
    OpenLayers.Control subclass. We'll follow the OpenLayers convention of adding
    the subclass to the OpenLayers namespace, by calling our new control OpenLayers.
    Control.Click. Once we've defined our new control, we can create an instance of
    the control and add it to the map so that the control can respond to mouse clicks.

    All of this has to be done in JavaScript. The code can be a bit confusing, so let's take
    this one step at a time. Edit your selectFeature.html file and add the following
    code to the <script> tag, immediately before your init() function:

    .. code-block:: js

        OpenLayers.Control.Click = OpenLayers.Class(
            OpenLayers.Control, {
                defaultHandlerOptions: {
                    'single': true,
                    'double': false,
                    'pixelTolerance' : 0,
                    'stopSingle': false,
                    'stopDouble': false
                },
                initialize: function(options) {
                    this.handlerOptions = OpenLayers.Util.extend(
                        {}, this.defaultHandlerOptions);
                    OpenLayers.Control.prototype.initialize.apply(
                        this, arguments);
                    this.handler = new OpenLayers.Handler.Click(
                        this, {'click' : this.onClick}, this.handlerOptions);
                },

                onClick: function(e) {
                        alert("click")
                }
            }
        );

    Don't worry too much about the details here—the initialize() function is
    a bit of black magic that creates a new OpenLayers.Control.Click instance
    and sets it up to run as an OpenLayers control. What is interesting to us is the
    defaultHandlerOptions dictionary, and the onClick() function.

    The defaultHandlerOptions dictionary tells OpenLayers how you want the
    click handler to respond to mouse clicks. In this case, we want to respond to single
    clicks, but not double clicks (as these are used to zoom further in to the map).

    The onClick() function is actually a JavaScript method for our OpenLayers.
    Control.Click class. This method will be called when the user clicks on the
    map—at the moment, all we're doing is displaying an alert box with the message
    Click, but that's enough to ensure that the click control is working.

    Now that we've defined our new click control, let's add it to the map. Add the
    following lines immediately before the closing } for the init() function:

    .. code-block:: js

        var click = new OpenLayers.Control.Click();
        map.addControl(click);
        click.activate();

    As you can see, we create a new instance of our OpenLayers.Control.Click class,
    add it to the map, and activate it.

    With all this code written, we can now reload the Select Feature web page and see what happens when the user clicks on a map:

    .. image:: ./img/468-0.png
       :align: center

    So far so good. Notice that our click handler only intercepts single clicks; if you
    double-click on the map, it will still zoom in.

    .. note::

        If your map isn't working, you may have made a mistake typing in the JavaScript code. Open your browser's JavaScript console or log window, and reload the page. An error message will appear in this window if there is a problem with your JavaScript code.

    Let's now implement the real onClick() function to respond to the user's mouse-
    click. When the user clicks on the map, we're going to send the clicked-on latitude
    and longitude value to the server using an AJAX request. The server will return
    the URL of the edit feature page for the clicked-on feature, or an empty string if no
    feature was clicked on. If a URL was returned, we'll then redirect the user's web
    browser to that URL.

    To make the AJAX call, we're going to use the OpenLayers.Request.GET function,
    passing in a callback function which will be called when a response is received back
    from the server. Let's start by writing the AJAX call.

    Replace our dummy onClick() function with the following:

    .. code-block:: js

        onClick: function(e) {
            var coord = map.getLonLatFromViewPortPx(e.xy);
            var request = OpenLayers.Request.GET({
                url: "{{ find_feature_url }}",
                params: {shapefile_id : {{ shapefile.id }},
                         latitude: coord.lat,
                         longitude: coord.lon},
                callback : this.handleResponse
            });
        }

    This function does two things: it obtains the map coordinate that corresponds to
    the clicked-on point (by calling the map.getLonLatFromViewPortPx() method),
    and then it creates an OpenLayers.Request.GET object to send the AJAX request
    to the server and call the handleResponse() callback function when the response
    is received.

    Notice that the OpenLayers.Request.GET() function accepts a set of query
    parameters (in the params entry), as well as a URL to send the request to (in the url
    entry) and a callback function to call when the response is received (in the callback
    entry). We're using a template parameter, {{ find_feature_url }}, to select
    the URL to send the request to. This will be provided by our edit_shapeFile()
    view function when the template is loaded. When we make the request, the query
    parameters will consist of the record ID of the shapefile and the clicked-on latitude
    and longitude values.

    While we're editing the select_feature.html template, let's go ahead and implement the callback function. Add the following function to the end of the OpenLayers.Control.Click class definition (immediately below the closing } for the onClick() function)::

    .. code-block:: js

        handleResponse: function(request) {
            if (request.status != 200) {
                alert("Server returned a "+request.status+" error");
                return;
            };
            if (request.responseText != "") {
                window.location.href = request.responseText;
            };
        }

    .. note::

        Make sure you add a comma after the onClick() function's closing parenthesis, or you'll get a JavaScript error. Just as with Python, you need to add commas to separate dictionary entries in JavaScript.

    Even if you're not familiar with JavaScript, this function should be easy to
    understand: if the response didn't have a status value of 200, an error message is
    displayed. Otherwise, we check that the response text is not blank, and if so we
    redirect the user's web browser to that URL.

    Now that we've implemented our callback function, let's go back to our view module
    and define the find_feature_url parameter which will get passed to the template
    we've created. Edit the view.py module to add the following highlighted lines to the
    edit_shapefile() function::

    .. code-block:: js

        def edit_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            tms_url = "http://"+request.get_host()+"/tms/"
            find_feature_url = "http://" + request.get_host() \
                            + "/editor/find_feature"

            return render(request, "select_feature.html",
                         {'shapefile': shapefile,
                          'find_feature_url' : find_feature_url,
                          'tms_url': tms_url})        

    The find_feature_url template parameter will contain the URL the click handler
    will send its AJAX request to. This URL will look like the following:

    http://127.0.0.1:8000/editor/find_feature
    
    Our onClick() function will add shapefile_id, latitude and longitude
    query parameters to this request, so the AJAX request sent to the server will
    look like the following::

    http://127.0.0.1:8000/editor/find_feature?shapefile_id=1&latitude=-38.1674&longitude=176.2344
    
    With our click handler up and running, we're now ready to start implementing the
    find_feature() view function to respond to these AJAX requests.

实现查找特征视图
--------------------------------------
Implementing the find feature view

.. tab:: 中文

    We now need to write the view function which receives the AJAX request, checks
    to see which feature was clicked on (if any), and returns a suitable URL to use to
    redirect the user's web browser to the "edit" page for that clicked-on feature. To
    implement this, we're going to make use of GeoDjango's **spatial query functions**.

    Let's start by adding the find_feature view itself. To do this, edit views.py again
    and add the following placeholder code::

        def find_feature(request):
            return HttpResponse("")

    Returning an empty string tells our AJAX callback function that no feature was
    clicked on. We'll replace this with some proper spatial queries shortly. First, though,
    we need to add a URL pattern so that incoming requests will get forwarded to the
    find_feature() view function. Open up the editor application's urls.py module
    and add the following entry to the URL pattern list::

        url(r'^find_feature$', 'find_feature'),

    You should now be able to run the ShapeEditor, click on the **Edit** hyperlink for an
    uploaded shapefile, see a map showing the various features within the shapefile, and
    click somewhere on the map. In response, the system should do—absolutely nothing!
    This is because our find_feature() function is returning an empty string, so the
    system thinks that the user didn't click on a feature and so ignores the mouse-click.

    .. note::

        In this case, "absolutely nothing" is good news. As long as no error messages are being displayed, either at the Python or JavaScript level, this tells us that the AJAX code is running correctly. So go ahead and try this, even though nothing happens, just to make sure that you haven't got any bugs in your code. You should see the AJAX calls in the list of incoming HTTP requests being received by the server.

    Before we implement the find_feature() function, let's take a step back and
    think what it means for the user to "click on" a feature's geometry. The shapeEditor
    supports a complete range of possible geometry types: Point, LineString, Polygon,
    MultiPoint, MultiLineString, MultiPolygon, and GeometryCollection. Identifying if
    the user clicked on a Polygon or MultiPolygon feature is straightforward enough: we
    simply see if the clicked-on point is inside the polygon's bounds. But because lines
    and points have no interior (their area will always be zero), a given coordinate could
    never be "inside" a Point or a LineString geometry. It might get infinitely close, but
    the user can never actually click inside a Point or a LineString.

    This means that a spatial query of the form::

        SELECT * FROM features WHERE ST_Contains(feature.geometry,clickPt)

    This is not going to work, because the click point can never be inside a Point or
    a LineString geometry. Instead, we have to allow for the user clicking close to the
    feature rather than within it. To do this, we'll calculate a search radius, in map
    units, and then use the DWithin() spatial query function to find all features
    within the given search radius of the clicked-on point.

    Let's start by calculating the search radius. We know that the user might click
    anywhere on the Earth's surface, and that we are storing all our features in lat
    /long coordinates. We also know that the relationship between map coordinates
    (latitude/longitude values) and actual distances on the Earth's surface varies
    widely depending on whereabouts on the Earth you are: a degree at the equator
    equals a distance of 111 kilometers, while a degree in Sweden is only half that far.

    To allow for a consistent search radius everywhere in the world, we will use the
    PROJ.4 library to calculate the distance in map units given the clicked-on location
    and a desired linear distance. Let's add this function to our shared.utils module::

        def calc_search_radius(latitude, longitude, distance):
            geod = pyproj.Geod(ellps="WGS84")

            x,y,angle = geod.fwd(longitude, latitude, 0, distance)
            radius = y-latitude

            x,y,angle = geod.fwd(longitude, latitude, 90, distance)
            radius = max(radius, x-longitude)

            x,y,angle = geod.fwd(longitude, latitude, 180, distance)
            radius = max(radius, latitude-y)

            x,y,angle = geod.fwd(longitude, latitude, 270, distance)
            radius = max(radius, longitude-x)

            return radius

    This function calculates the distance, in map units, of a given linear distance
    measured in meters. It calculates the lat/long coordinates for four points directly
    north, south, east, and west of the starting location and the given number of meters
    away from that point. It then calculates the difference in latitude or longitude
    between the starting location and the end point:

    .. image:: ./img/473-0.png
       :align: center

    Finally, it takes the largest of these differences and returns it as the search radius,
    which is measured in degrees of latitude or longitude.
    
    Because our utils.py module is now using pyproj, add the following import
    statement to the top of this module::

        import pyproj
    
    With the calc_search_radius() function written, we can now use the DWithin()
    spatial query to identify all features close to the clicked-on location. The general
    process of doing this in GeoDjango is to use the filter() function to create a
    spatial query, as follows::

        query = Feature.objects.filter(geometry__dwithin=(pt, radius))

    This creates a query set that returns only the Feature objects which match the given
    criteria. GeoDjango cleverly adds support for spatial queries to Django's built-in
    filtering capabilities; in this case, the geometry__dwithin=(pt, radius) parameter
    tells GeoDjango to perform the dwithin() spatial query using the two supplied
    parameters on the field named geometry within the Feature object. Thus, this
    statement will be translated by GeoDjango into a spatial database query which looks
    something as follows::

        SELECT * from feature WHERE ST_DWithin(geometry, pt, radius)
    
    .. note::

        Note that the geometry__dwithin keyword parameter includes two underscore characters; Django uses a double-underscore to separate the field name from the filter function's name.
    
    Knowing this, and having the utils.calc_search_radius() function implemented,
    we can finally implement our find_feature() view function. Edit views.py and
    replace the body of the find_feature() function with the following::

        def find_feature(request):
            try:
                shapefile_id = int(request.GET['shapefile_id'])
                latitude     = float(request.GET['latitude'])
                longitude    = float(request.GET['longitude'])
                
                shapefile = Shapefile.objects.get(id=shapefile_id)
                pt = Point(longitude, latitude)
                radius = utils.calc_search_radius(latitude, longitude, 100)

                if shapefile.geom_type == "Point":
                    query = Feature.objects.filter(
                        geom_point__dwithin=(pt, radius))
                elif shapefile.geom_type in ["LineString", "MultiLineString"]:
                    query = Feature.objects.filter(
                        geom_multilinestring__dwithin=(pt, radius))
                elif shapefile.geom_type in ["Polygon", "MultiPolygon"]:
                    query = Feature.objects.filter(
                        geom_multipolygon__dwithin=(pt, radius))
                elif shapefile.geom_type == "MultiPoint":
                    query = Feature.objects.filter(
                        geom_multipoint__dwithin=(pt, radius))
                elif shapefile.geom_type == "GeometryCollection":
                    query = feature.objects.filter(
                        geom_geometrycollection__dwithin=(pt, radius))
                else:
                    print "Unsupported geometry: " + shapefile.geom_type
                    return HttpResponse("")

                if query.count() != 1:
                    return HttpResponse("")

                feature = query[0]
                return HttpResponse("/editor/edit_feature/" +
                                    str(shapefile_id)+"/"+str(feature.id))
            except:
                traceback.print_exc()
                return HttpResponse("")

    There's a lot here, so let's take this one step at a time. First off, we've wrapped all our
    code inside a try...except statement::

        def find_feature(request):
            try:
                ...
            except:
                traceback.print_exc()
                return HttpResponse("")

    This is the same technique we used when implementing the Tile Map Server; it
    means that any Python errors in your code will be displayed in the web server's
    log, and the AJAX function will return gracefully rather than crashing.

    We then extract the supplied query parameters, converting them from strings to
    numbers, load the desired Shapefile object, create a GeoDjango Point object out
    of the clicked-on coordinates, and calculate the search radius in degrees::

        shapefile_id = int(request.GET['shapefile_id'])
        latitude     = float(request.GET['latitude'])
        longitude    = float(request.GET['longitude'])

        shapefile = Shapefile.objects.get(id=shapefile_id)
        pt = Point(longitude, latitude)
        radius = utils.calc_search_radius(latitude, longitude, 100)

    Note that we use a hardwired search radius of 100 meters; this is enough to let the
    user select a point or line feature by clicking close to it, without being so large that
    the user might accidentally click on the wrong feature.

    With this done, we're now ready to perform the spatial query. Because our Feature
    object has separate fields to hold each different type of geometry, we have to build
    the query based on the geometry's type::

        if shapefile.geom_type == "Point":
            query = Feature.objects.filter(
                        geom_point__dwithin=(pt, radius))
        elif shapefile.geom_type in ["LineString", "MultiLineString"]:
            query = Feature.objects.filter(
                        geom_multilinestring__dwithin=(pt, radius))
        elif shapefile.geom_type in ["Polygon", "MultiPolygon"]:
            query = Feature.objects.filter(
                        geom_multipolygon__dwithin=(pt, radius))
        elif shapefile.geom_type == "MultiPoint":
            query = Feature.objects.filter(
                        geom_multipoint__dwithin=(pt, radius))
        elif shapefile.geom_type == "GeometryCollection":
            query = feature.objects.filter(
                        geom_geometrycollection__dwithin=(pt, radius))
        else:
            print "Unsupported geometry: " + shapefile.geom_type
            return HttpResponse("")

    In each case, we choose the appropriate geometry field, and use __dwithin to
    perform a spatial query on the appropriate field in the Feature object.

    Once we've created the appropriate spatial query, we simply check to see if the query
    returned exactly one Feature. If not, we return an empty string back to the AJAX
    handler's callback function, to tell it that the user did not click on a feature::

        if query.count() != 1:
            return HttpResponse("")

    If there was exactly one matching feature, we get the clicked-on feature and use it
    to build a URL redirecting the user's web browser to the "edit feature" URL for the
    clicked-on feature::

        feature = query[0]
        return HttpResponse("/shape-editor/editFeature/" +
                            str(shapefile_id)+"/"+str(feature.id))

    After typing in the previous code, add the following import statements to the top
    of the views.py module::

        import traceback
        from django.contrib.gis.geos import Point
        from shapeEditor.shared.models import Feature
        from shapeEditor.shared import utils

    This completes the find_feature() view function. Save your changes, run the
    Django web server if it is not already running, and try clicking on a shapefile's
    features. If you click on the ocean, nothing should happen—but if you click on
    a feature, you should see your web browser redirected to a URL of the form::

    http://127.0.0.1:8000/shape-editor/editFeature/X/Y
    
    where X is the record ID of the shapefile, and Y is the record ID of the clicked-on
    feature. Of course, at this stage you'll get a **Page Not Found** error, because you haven't
    written that page yet. But at least you can click on a feature to select it, which is a major
    milestone in the development of the ShapeEditor application. Congratulations!

.. tab:: 英文

    We now need to write the view function which receives the AJAX request, checks
    to see which feature was clicked on (if any), and returns a suitable URL to use to
    redirect the user's web browser to the "edit" page for that clicked-on feature. To
    implement this, we're going to make use of GeoDjango's **spatial query functions**.

    Let's start by adding the find_feature view itself. To do this, edit views.py again
    and add the following placeholder code::

        def find_feature(request):
            return HttpResponse("")

    Returning an empty string tells our AJAX callback function that no feature was
    clicked on. We'll replace this with some proper spatial queries shortly. First, though,
    we need to add a URL pattern so that incoming requests will get forwarded to the
    find_feature() view function. Open up the editor application's urls.py module
    and add the following entry to the URL pattern list::

        url(r'^find_feature$', 'find_feature'),

    You should now be able to run the ShapeEditor, click on the **Edit** hyperlink for an
    uploaded shapefile, see a map showing the various features within the shapefile, and
    click somewhere on the map. In response, the system should do—absolutely nothing!
    This is because our find_feature() function is returning an empty string, so the
    system thinks that the user didn't click on a feature and so ignores the mouse-click.

    .. note::

        In this case, "absolutely nothing" is good news. As long as no error messages are being displayed, either at the Python or JavaScript level, this tells us that the AJAX code is running correctly. So go ahead and try this, even though nothing happens, just to make sure that you haven't got any bugs in your code. You should see the AJAX calls in the list of incoming HTTP requests being received by the server.

    Before we implement the find_feature() function, let's take a step back and
    think what it means for the user to "click on" a feature's geometry. The shapeEditor
    supports a complete range of possible geometry types: Point, LineString, Polygon,
    MultiPoint, MultiLineString, MultiPolygon, and GeometryCollection. Identifying if
    the user clicked on a Polygon or MultiPolygon feature is straightforward enough: we
    simply see if the clicked-on point is inside the polygon's bounds. But because lines
    and points have no interior (their area will always be zero), a given coordinate could
    never be "inside" a Point or a LineString geometry. It might get infinitely close, but
    the user can never actually click inside a Point or a LineString.

    This means that a spatial query of the form::

        SELECT * FROM features WHERE ST_Contains(feature.geometry,clickPt)

    This is not going to work, because the click point can never be inside a Point or
    a LineString geometry. Instead, we have to allow for the user clicking close to the
    feature rather than within it. To do this, we'll calculate a search radius, in map
    units, and then use the DWithin() spatial query function to find all features
    within the given search radius of the clicked-on point.

    Let's start by calculating the search radius. We know that the user might click
    anywhere on the Earth's surface, and that we are storing all our features in lat
    /long coordinates. We also know that the relationship between map coordinates
    (latitude/longitude values) and actual distances on the Earth's surface varies
    widely depending on whereabouts on the Earth you are: a degree at the equator
    equals a distance of 111 kilometers, while a degree in Sweden is only half that far.

    To allow for a consistent search radius everywhere in the world, we will use the
    PROJ.4 library to calculate the distance in map units given the clicked-on location
    and a desired linear distance. Let's add this function to our shared.utils module::

        def calc_search_radius(latitude, longitude, distance):
            geod = pyproj.Geod(ellps="WGS84")

            x,y,angle = geod.fwd(longitude, latitude, 0, distance)
            radius = y-latitude

            x,y,angle = geod.fwd(longitude, latitude, 90, distance)
            radius = max(radius, x-longitude)

            x,y,angle = geod.fwd(longitude, latitude, 180, distance)
            radius = max(radius, latitude-y)

            x,y,angle = geod.fwd(longitude, latitude, 270, distance)
            radius = max(radius, longitude-x)

            return radius

    This function calculates the distance, in map units, of a given linear distance
    measured in meters. It calculates the lat/long coordinates for four points directly
    north, south, east, and west of the starting location and the given number of meters
    away from that point. It then calculates the difference in latitude or longitude
    between the starting location and the end point:

    .. image:: ./img/473-0.png
       :align: center

    Finally, it takes the largest of these differences and returns it as the search radius,
    which is measured in degrees of latitude or longitude.
    
    Because our utils.py module is now using pyproj, add the following import
    statement to the top of this module::

        import pyproj
    
    With the calc_search_radius() function written, we can now use the DWithin()
    spatial query to identify all features close to the clicked-on location. The general
    process of doing this in GeoDjango is to use the filter() function to create a
    spatial query, as follows::

        query = Feature.objects.filter(geometry__dwithin=(pt, radius))

    This creates a query set that returns only the Feature objects which match the given
    criteria. GeoDjango cleverly adds support for spatial queries to Django's built-in
    filtering capabilities; in this case, the geometry__dwithin=(pt, radius) parameter
    tells GeoDjango to perform the dwithin() spatial query using the two supplied
    parameters on the field named geometry within the Feature object. Thus, this
    statement will be translated by GeoDjango into a spatial database query which looks
    something as follows::

        SELECT * from feature WHERE ST_DWithin(geometry, pt, radius)
    
    .. note::

        Note that the geometry__dwithin keyword parameter includes two underscore characters; Django uses a double-underscore to separate the field name from the filter function's name.
    
    Knowing this, and having the utils.calc_search_radius() function implemented,
    we can finally implement our find_feature() view function. Edit views.py and
    replace the body of the find_feature() function with the following::

        def find_feature(request):
            try:
                shapefile_id = int(request.GET['shapefile_id'])
                latitude     = float(request.GET['latitude'])
                longitude    = float(request.GET['longitude'])
                
                shapefile = Shapefile.objects.get(id=shapefile_id)
                pt = Point(longitude, latitude)
                radius = utils.calc_search_radius(latitude, longitude, 100)

                if shapefile.geom_type == "Point":
                    query = Feature.objects.filter(
                        geom_point__dwithin=(pt, radius))
                elif shapefile.geom_type in ["LineString", "MultiLineString"]:
                    query = Feature.objects.filter(
                        geom_multilinestring__dwithin=(pt, radius))
                elif shapefile.geom_type in ["Polygon", "MultiPolygon"]:
                    query = Feature.objects.filter(
                        geom_multipolygon__dwithin=(pt, radius))
                elif shapefile.geom_type == "MultiPoint":
                    query = Feature.objects.filter(
                        geom_multipoint__dwithin=(pt, radius))
                elif shapefile.geom_type == "GeometryCollection":
                    query = feature.objects.filter(
                        geom_geometrycollection__dwithin=(pt, radius))
                else:
                    print "Unsupported geometry: " + shapefile.geom_type
                    return HttpResponse("")

                if query.count() != 1:
                    return HttpResponse("")

                feature = query[0]
                return HttpResponse("/editor/edit_feature/" +
                                    str(shapefile_id)+"/"+str(feature.id))
            except:
                traceback.print_exc()
                return HttpResponse("")

    There's a lot here, so let's take this one step at a time. First off, we've wrapped all our
    code inside a try...except statement::

        def find_feature(request):
            try:
                ...
            except:
                traceback.print_exc()
                return HttpResponse("")

    This is the same technique we used when implementing the Tile Map Server; it
    means that any Python errors in your code will be displayed in the web server's
    log, and the AJAX function will return gracefully rather than crashing.

    We then extract the supplied query parameters, converting them from strings to
    numbers, load the desired Shapefile object, create a GeoDjango Point object out
    of the clicked-on coordinates, and calculate the search radius in degrees::

        shapefile_id = int(request.GET['shapefile_id'])
        latitude     = float(request.GET['latitude'])
        longitude    = float(request.GET['longitude'])

        shapefile = Shapefile.objects.get(id=shapefile_id)
        pt = Point(longitude, latitude)
        radius = utils.calc_search_radius(latitude, longitude, 100)

    Note that we use a hardwired search radius of 100 meters; this is enough to let the
    user select a point or line feature by clicking close to it, without being so large that
    the user might accidentally click on the wrong feature.

    With this done, we're now ready to perform the spatial query. Because our Feature
    object has separate fields to hold each different type of geometry, we have to build
    the query based on the geometry's type::

        if shapefile.geom_type == "Point":
            query = Feature.objects.filter(
                        geom_point__dwithin=(pt, radius))
        elif shapefile.geom_type in ["LineString", "MultiLineString"]:
            query = Feature.objects.filter(
                        geom_multilinestring__dwithin=(pt, radius))
        elif shapefile.geom_type in ["Polygon", "MultiPolygon"]:
            query = Feature.objects.filter(
                        geom_multipolygon__dwithin=(pt, radius))
        elif shapefile.geom_type == "MultiPoint":
            query = Feature.objects.filter(
                        geom_multipoint__dwithin=(pt, radius))
        elif shapefile.geom_type == "GeometryCollection":
            query = feature.objects.filter(
                        geom_geometrycollection__dwithin=(pt, radius))
        else:
            print "Unsupported geometry: " + shapefile.geom_type
            return HttpResponse("")

    In each case, we choose the appropriate geometry field, and use __dwithin to
    perform a spatial query on the appropriate field in the Feature object.

    Once we've created the appropriate spatial query, we simply check to see if the query
    returned exactly one Feature. If not, we return an empty string back to the AJAX
    handler's callback function, to tell it that the user did not click on a feature::

        if query.count() != 1:
            return HttpResponse("")

    If there was exactly one matching feature, we get the clicked-on feature and use it
    to build a URL redirecting the user's web browser to the "edit feature" URL for the
    clicked-on feature::

        feature = query[0]
        return HttpResponse("/shape-editor/editFeature/" +
                            str(shapefile_id)+"/"+str(feature.id))

    After typing in the previous code, add the following import statements to the top
    of the views.py module::

        import traceback
        from django.contrib.gis.geos import Point
        from shapeEditor.shared.models import Feature
        from shapeEditor.shared import utils

    This completes the find_feature() view function. Save your changes, run the
    Django web server if it is not already running, and try clicking on a shapefile's
    features. If you click on the ocean, nothing should happen—but if you click on
    a feature, you should see your web browser redirected to a URL of the form::

    http://127.0.0.1:8000/shape-editor/editFeature/X/Y
    
    where X is the record ID of the shapefile, and Y is the record ID of the clicked-on
    feature. Of course, at this stage you'll get a **Page Not Found** error, because you haven't
    written that page yet. But at least you can click on a feature to select it, which is a major
    milestone in the development of the ShapeEditor application. Congratulations!

