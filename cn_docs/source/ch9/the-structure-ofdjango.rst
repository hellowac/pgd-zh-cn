Django 应用程序的结构
============================================

The structure of a Django application

.. tab:: 中文

    While a complete tutorial on Django is beyond the scope of this book, it is worth
    spending a few minutes becoming familiar with how Django works. In Django,
    you start by building a **project** that contains one or more **applications**. Each
    project has a single database that is shared by the applications within it:

    .. image:: ./img/380-0.png
       :align: center

    Django comes with a large number of built-in applications that you can include as
    part of your project, including the following:

    - An **authentication** system supporting user accounts, groups, permissions, and authenticated sessions
    - An **admin** interface, allowing the user to view and edit data
    - A **markup** application supporting lightweight text markup languages including RestructuredText and Markdown
    - A **messages** framework for sending and receiving messages
    - A **sessions** system for keeping track of anonymous (non-authenticated) sessions
    - A **sitemaps** framework for generating site maps
    - A **syndication** system for generating RSS and ATOM feeds

    The GeoDjango extension is implemented as yet another application within Django
    that you install when you wish to use it.

    .. note::

        Note that applications in Django tend to be fairly small and discrete. Often, an application will implement just one aspect of your system. For example, a complex project may have a shared application that defines the shared database tables and commonly-used modules, an editor application that allows users to edit data, an importExport application that handles importing and exporting, and a report application for generating reports. These applications work together to implement the project—for example, the report application may make use of data stored in the shared application's database models, and the editor application may redirect the user to an importExport view when they click on the **Import Data** hyperlink.
    
    The project has a **settings** file, which you to use to configure the project as
    a whole. These settings include a list of the applications you want to include
    in the project, which database to use, as well as various other project- and
    application-specific settings.

    As we saw in the previous chapter, a Django application has three main components:

    .. image:: ./img/382-0.png
       :align: center

    The **models** define your application's data structures, the **views** contain your
    application's business logic, and the **templates** are used to control how information
    is presented to the user. These correspond to the data, application, and presentation
    tiers within a traditional web application stack. Let's take a closer look at each of
    these in turn.

.. tab:: 英文


模型
----------------------
Models

.. tab:: 中文

    Because Django provides an object-relational mapper on top of the database, you don't
    have to deal with SQL directly. Instead, you define a **model** that describes the data you
    want to store, and Django will automatically map that model onto the database:

    .. image:: ./img/382-1.png
       :align: center

    This high-level interface to the database is a major reason why working in Django is
    so efficient.

    .. note::

        In the ShapeEditor, the database objects we looked at earlier (*Shapefile*, *Attribute*, *Feature*, and *AttributeValue*) are all models, and will be defined in a file named models.py that holds the ShapeEditor's models.

.. tab:: 英文

    Because Django provides an object-relational mapper on top of the database, you don't
    have to deal with SQL directly. Instead, you define a **model** that describes the data you
    want to store, and Django will automatically map that model onto the database:

    .. image:: ./img/382-1.png
       :align: center

    This high-level interface to the database is a major reason why working in Django is
    so efficient.

    .. note::

        In the ShapeEditor, the database objects we looked at earlier (*Shapefile*, *Attribute*, *Feature*, and *AttributeValue*) are all models, and will be defined in a file named models.py that holds the ShapeEditor's models.

视图
----------------------
Views

.. tab:: 中文

    In Django, a view is a Python function which responds when a given URL is called.
    For example, the ShapeEditor application will respond to the /editFeature URL by
    allowing the user to edit a feature; the function which handles this URL is called the
    "edit feature" view, and will be defined like this::

        def editFeature(request, shapefile_id, feature_id):

    In general, an application's views will be defined in a Python module named,
    as you might expect, views.py. Not all of the application's views have to be
    defined in this file, but it is common to use this file (or a Python package) to
    hold your application's views.

    At its simplest, a view might return the HTML text to be displayed, like this::
    
        def myView(request):
            return HttpResponse("Hello World")
    
    Of course, views will generally be a lot more complicated, dealing with database
    objects and returning very sophisticated HTML pages. Views can also return other
    types of data, for example to display an image or download a file, or to respond to
    an incoming AJAX request.

.. tab:: 英文

    In Django, a view is a Python function which responds when a given URL is called.
    For example, the ShapeEditor application will respond to the /editFeature URL by
    allowing the user to edit a feature; the function which handles this URL is called the
    "edit feature" view, and will be defined like this::

        def editFeature(request, shapefile_id, feature_id):

    In general, an application's views will be defined in a Python module named,
    as you might expect, views.py. Not all of the application's views have to be
    defined in this file, but it is common to use this file (or a Python package) to
    hold your application's views.

    At its simplest, a view might return the HTML text to be displayed, like this::
    
        def myView(request):
            return HttpResponse("Hello World")
    
    Of course, views will generally be a lot more complicated, dealing with database
    objects and returning very sophisticated HTML pages. Views can also return other
    types of data, for example to display an image or download a file, or to respond to
    an incoming AJAX request.

URL 调度
----------------------
URL dispatching

.. tab:: 中文

    When an incoming HTTP request is sent to a URL within the web application, that request is forwarded to the view in the following way:

    .. image:: ./img/384-0.png
       :align: center

    The web server receives the request and passes it on to a URL dispatcher,
    which in Django parlance is called a **URLConf**. This is a Python module that
    maps incoming URLs to views. The view function then processes the request
    and returns a response, which is passed back to the web server so that it can
    be sent back to the user's web browser.

    The URLConf module is normally named *urls.py*, and consists of a list of regular
    expression patterns along with the views these patterns map to. For example,
    here is a copy of part of the ShapeEditor's *urls.py* file::

        from django.conf.urls.defaults import *

        urlpatterns = patterns('geodjango.shapeEditor.views',
                (r'^shape-editor$',
                'listShapefiles'),
            ...
        )

    This tells Django that any URL which matches the pattern ^shape-editor$ (that is, a
    URL consisting only of the text shape-editor) will be mapped to the listShapefiles
    function, which can be found in the geodjango.shapeEditor.views module.

    .. note::

        This is a slight simplification: the geodjango.shapeEditor. views entry in the preceding code example is actually a prefix, which is applied to the view name. Prefixes can be anything you like, so long as the prefix plus a period plus the view name yields a fully-qualified reference to your view function.
    
    As well as simply mapping URLs to view functions, the URLConf module also lets
    you define **parameters** to be passed to the view. Take, for example, the following
    URL mapping::

        (r'^shape-editor/edit/(?P<shapefile_id>\d+)$',
        'editShapefile'),

    The syntax is a bit complicated, thanks to the use of regular expression patterns,
    but the basic idea is that this entry in the URLConf will match any URL of the
    following form::

        shape-editor/edit/NNNN
    
    In this URL, NNNN is a sequence of one or more digits. The actual text used for
    NNNN will be passed to the editShapefile() view function as an extra keyword
    parameter named shapefile_id. This means that the view function would be
    defined like this::

        def editShapefile(request, shapefile_id):

    While the URL mapping does require you to be familiar with regular expressions,
    it is extremely flexible, and allows you to define exactly which view will be called
    for any given incoming URL, as well as allowing you to include parts of the URL
    as parameters to the view function.

    .. note::

        Remember that Django allows multiple applications to exist within
        a single project. Because of this, the URLConf module belongs to the
        project, and contains mappings for all the project's applications in one
        place. Applications often define their own URLConf modules, which
        are then imported by the project's URLConf to insert them into the
        overall system. For example, you might have an application called
        "editor" that defines its own URLs (/add, /delete, and so on). The
        project's URLConf might include the editor application's URLs using
        the /editor prefix. This would have the effect of associating the
        editor's add() view function with the overall URL /editor/add.
        Notice how the editor application only defines its own URLs—it
        doesn't know about the /editor prefix—and the project then
        includes all those URLs under the appropriate prefix. This allows
        different applications to coexist within a single project, without
        interfering with (or even knowing about) each other's URLs.

.. tab:: 英文

    When an incoming HTTP request is sent to a URL within the web application, that request is forwarded to the view in the following way:

    .. image:: ./img/384-0.png
       :align: center

    The web server receives the request and passes it on to a URL dispatcher,
    which in Django parlance is called a **URLConf**. This is a Python module that
    maps incoming URLs to views. The view function then processes the request
    and returns a response, which is passed back to the web server so that it can
    be sent back to the user's web browser.

    The URLConf module is normally named *urls.py*, and consists of a list of regular
    expression patterns along with the views these patterns map to. For example,
    here is a copy of part of the ShapeEditor's *urls.py* file::

        from django.conf.urls.defaults import *

        urlpatterns = patterns('geodjango.shapeEditor.views',
                (r'^shape-editor$',
                'listShapefiles'),
            ...
        )

    This tells Django that any URL which matches the pattern ^shape-editor$ (that is, a
    URL consisting only of the text shape-editor) will be mapped to the listShapefiles
    function, which can be found in the geodjango.shapeEditor.views module.

    .. note::

        This is a slight simplification: the geodjango.shapeEditor. views entry in the preceding code example is actually a prefix, which is applied to the view name. Prefixes can be anything you like, so long as the prefix plus a period plus the view name yields a fully-qualified reference to your view function.
    
    As well as simply mapping URLs to view functions, the URLConf module also lets
    you define **parameters** to be passed to the view. Take, for example, the following
    URL mapping::

        (r'^shape-editor/edit/(?P<shapefile_id>\d+)$',
        'editShapefile'),

    The syntax is a bit complicated, thanks to the use of regular expression patterns,
    but the basic idea is that this entry in the URLConf will match any URL of the
    following form::

        shape-editor/edit/NNNN
    
    In this URL, NNNN is a sequence of one or more digits. The actual text used for
    NNNN will be passed to the editShapefile() view function as an extra keyword
    parameter named shapefile_id. This means that the view function would be
    defined like this::

        def editShapefile(request, shapefile_id):

    While the URL mapping does require you to be familiar with regular expressions,
    it is extremely flexible, and allows you to define exactly which view will be called
    for any given incoming URL, as well as allowing you to include parts of the URL
    as parameters to the view function.

    .. note::

        Remember that Django allows multiple applications to exist within
        a single project. Because of this, the URLConf module belongs to the
        project, and contains mappings for all the project's applications in one
        place. Applications often define their own URLConf modules, which
        are then imported by the project's URLConf to insert them into the
        overall system. For example, you might have an application called
        "editor" that defines its own URLs (/add, /delete, and so on). The
        project's URLConf might include the editor application's URLs using
        the /editor prefix. This would have the effect of associating the
        editor's add() view function with the overall URL /editor/add.
        Notice how the editor application only defines its own URLs—it
        doesn't know about the /editor prefix—and the project then
        includes all those URLs under the appropriate prefix. This allows
        different applications to coexist within a single project, without
        interfering with (or even knowing about) each other's URLs.


模板
----------------------
Templates

.. tab:: 中文

    To simplify the creation of complex HTML pages, Django provides a sophisticated
    templating system. A **template** is a text file that is processed to generate a web
    page by taking variables from the view and processing them to generate the page
    dynamically. For example, here is a snippet from the *listShapefiles.html*
    template used by the ShapeEditor::

        <b>Available Shapefiles:</b>
        <table>
            {% for shapefile in shapefiles %}
            <tr>
                <td>{{ shapefile.filename }}</td>
                ...
            </tr>
            {% endfor %}
        </table>

    As you can see, most of the template is simply HTML, with a few programming
    constructs added. In this case, we loop through the *shapefiles* list, creating a table
    row for each shapefile, and display (among other things) the shapefile's filename.

    To use this template, the view function might look something like this::

        def myView(request):
            shapefiles = ...
            return render_to_response("listShapefiles.html",
                                      {'shapefiles' : shapefiles})

    As you can see, the render_to_response() function takes the name of the template,
    and a dictionary containing the variables to use when processing the template. The
    result is an HTML page, which will be displayed to the end user.

    All of the templates for an application are generally stored in a
    directory named templates within the application's directory.
    
    Django also includes a library for working with data-entry forms. A form is defined
    as a Python class defining the various fields to be entered, along with data validation
    and other behaviors associated with the form. For example, here is the "import
    shapefile" form used by the ShapeEditor::
    
        class ImportShapefileForm(forms.Form):
            import_file = forms.FileField(label="Select a Shapefile")
            character_encoding = forms.ChoiceField(...)

    forms.FileField is a standard Django form field for handling file uploads, while
    forms.ChoiceField is a standard form field for displaying a drop-down menu of
    available choices. It's easy to use a form within a Django view; for example::

        def importShapefile(request):
            if request.method == "GET":
                form = ImportShapefileForm()
                return render_to_response("importShapefile.html",
                                          {'form' : form})
            elif request.method == "POST":
                form = ImportShapefileForm(request.POST,
                                           request.FILES)
                if form.is_valid():
                    shapefile = request.FILES['import_file']
                    encoding = request.POST['character_encoding']
                    ...
                else:
                    return render_to_response("importShapefile.html",
                                              {'form' : form})

    If the user is submitting the form (request.method == "POST"), we check that the
    form's contents are valid and process them. Otherwise, we build a new form from
    scratch. Notice that the render_to_response() function is called with the form
    object as a parameter to be passed to the template. This template will look something
    like the following::

        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
            <body>
                <h1>Import Shapefile</h1>
                <form enctype="multipart/form-data" method="post"
                      action="import">
                    {{ form.as_p }}
                <input type="submit" value="Submit"/>
            </form>
            </body>
        </html>

    The {{ form.as_p }} instruction renders the form in HTML format (embedded
    within a <p> tag) and includes it in the template at that point.

    Forms are especially important when working with GeoDjango, because the map
    editor widgets are implemented as part of a form.
    
    This completes our whirlwind tour of Django. It's certainly not comprehensive, and
    you are encouraged to follow the tutorials on the Django website to learn more, but
    we have covered enough of the core concepts for you to understand what is going
    on as we implement the ShapeEditor. Without further ado, let's start implementing
    the ShapeEditor by setting up a PostGIS database for our application to use.

.. tab:: 英文

    To simplify the creation of complex HTML pages, Django provides a sophisticated
    templating system. A **template** is a text file that is processed to generate a web
    page by taking variables from the view and processing them to generate the page
    dynamically. For example, here is a snippet from the *listShapefiles.html*
    template used by the ShapeEditor::

        <b>Available Shapefiles:</b>
        <table>
            {% for shapefile in shapefiles %}
            <tr>
                <td>{{ shapefile.filename }}</td>
                ...
            </tr>
            {% endfor %}
        </table>

    As you can see, most of the template is simply HTML, with a few programming
    constructs added. In this case, we loop through the *shapefiles* list, creating a table
    row for each shapefile, and display (among other things) the shapefile's filename.

    To use this template, the view function might look something like this::

        def myView(request):
            shapefiles = ...
            return render_to_response("listShapefiles.html",
                                      {'shapefiles' : shapefiles})

    As you can see, the render_to_response() function takes the name of the template,
    and a dictionary containing the variables to use when processing the template. The
    result is an HTML page, which will be displayed to the end user.

    All of the templates for an application are generally stored in a
    directory named templates within the application's directory.
    
    Django also includes a library for working with data-entry forms. A form is defined
    as a Python class defining the various fields to be entered, along with data validation
    and other behaviors associated with the form. For example, here is the "import
    shapefile" form used by the ShapeEditor::
    
        class ImportShapefileForm(forms.Form):
            import_file = forms.FileField(label="Select a Shapefile")
            character_encoding = forms.ChoiceField(...)

    forms.FileField is a standard Django form field for handling file uploads, while
    forms.ChoiceField is a standard form field for displaying a drop-down menu of
    available choices. It's easy to use a form within a Django view; for example::

        def importShapefile(request):
            if request.method == "GET":
                form = ImportShapefileForm()
                return render_to_response("importShapefile.html",
                                          {'form' : form})
            elif request.method == "POST":
                form = ImportShapefileForm(request.POST,
                                           request.FILES)
                if form.is_valid():
                    shapefile = request.FILES['import_file']
                    encoding = request.POST['character_encoding']
                    ...
                else:
                    return render_to_response("importShapefile.html",
                                              {'form' : form})

    If the user is submitting the form (request.method == "POST"), we check that the
    form's contents are valid and process them. Otherwise, we build a new form from
    scratch. Notice that the render_to_response() function is called with the form
    object as a parameter to be passed to the template. This template will look something
    like the following::

        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
            <body>
                <h1>Import Shapefile</h1>
                <form enctype="multipart/form-data" method="post"
                      action="import">
                    {{ form.as_p }}
                <input type="submit" value="Submit"/>
            </form>
            </body>
        </html>

    The {{ form.as_p }} instruction renders the form in HTML format (embedded
    within a <p> tag) and includes it in the template at that point.

    Forms are especially important when working with GeoDjango, because the map
    editor widgets are implemented as part of a form.
    
    This completes our whirlwind tour of Django. It's certainly not comprehensive, and
    you are encouraged to follow the tutorials on the Django website to learn more, but
    we have covered enough of the core concepts for you to understand what is going
    on as we implement the ShapeEditor. Without further ado, let's start implementing
    the ShapeEditor by setting up a PostGIS database for our application to use.

