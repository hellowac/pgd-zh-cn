实现“列出 Shapefile”视图
============================================

Implementing the "list shapefiles" view

.. tab:: 中文

    When the user first opens the ShapeEditor application, we want them to see a list
    of the previously-uploaded shapefiles, with "import", "edit", "export", and "delete"
    options. Let's build this list view, which acts as the starting point for the entire
    ShapeEditor application.

    This view is going to be part of the "editor" application, so we first need to create
    this application within our Django project. To do this, cd into the shapeEditor
    project directory and type the following::

        python manage.py startapp editor

    As usual, Django places the application in the top-level directory, making it
    a reusable application. We need to move it into our shapeEditor directory
    so that it becomes specific to our project. Either move the directory manually,
    or use the following terminal command::

        mv editor shapeEditor
    
    We now need to add our new application to the project. Edit the settings.py
    module, and add the following line to the end of the INSTALLED_APPS list::
    
        'shapeEditor.editor',

    Because the editor is going to support various URLs, we will want to give the editor
    its own URLConf module. To do this, create a new file in the shapeEditor/editor
    directory named urls.py, and enter the following into this file::

        # URLConf for the shapeEditor.editor application.
        
        from django.conf.urls import patterns, url
        
        urlpatterns = patterns('shapeEditor.editor.views',
            url(r'^$', 'list_shapefiles'),
        )

    This URLConf is going to handle all the URLs for the editor application. At present
    we have just one entry, that maps the top-level URL (defined using the r'^$'
    regular expression, which matches an empty string) to the list_shapefiles()
    view function.

    We next need to edit the top-level urls.py module so that the editor application's
    URLs will be included in the project. Change the top-level urls.py module (at
    shapeEditor/urls.py) to look like this::

        from django.conf.urls.defaults import patterns, include, url
        from django.contrib.gis import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^editor/', include('shapeEditor.editor.urls')),
        )
        urlpatterns += patterns('',
            url(r'^admin/', include(admin.site.urls)),
        )

    Notice that we've now got two separate sets of URL patterns, one that places all
    of the shapeEditor.editor application's views into the editor URL, and another
    that places the django.contrib.gis.admin application's views into the admin
    URL. This is a very convenient way of splitting up a project's URLs, so that each
    application has its own section within the URL namespace.

    Now that we've set up our URL, let's write the view to go with it. We'll start by
    creating a very simple implementation of the list_shapefiles() view, just to
    make sure it works. Open the views.py module in the editor directory and
    change this file to look like this::

        from django.http import HttpResponse
        
        def list_shapefiles(request):
            return HttpResponse("in list_shapefiles")

    If it isn't already running, start up the GeoDjango web server. To do this,
    open a command-line window, cd into the geodjango project directory,
    and type the following::

        python manage.py runserver

    Then open your web browser and navigate to the following URL:
    
    http://127.0.0.1:8000/editor
    
    All going well, you should see in **list_shapefiles** appear on the web page. This tells
    you that you've successfully created the list_shapefiles() view function and
    have correctly set up the URLConf mappings to point to this view.

    We now want to create the view which will display the list of shapefiles. To do so,
    we'll make use of a Django template. Edit the views.py module again, and change
    this module's contents to look like this::

        from django.http import HttpResponse
        from django.shortcuts import render
        from shapeEditor.shared.models import Shapefile

        def list_shapefiles(request):
            shapefiles = Shapefile.objects.all().order_by("filename")
            return render(request, "list_shapefiles.html",
                          {'shapefiles' : shapefiles})

    The list_shapefiles() view function now does two things:

    - It loads the list of all Shapefile objects from the database into memory, sorted by filename
    - It passes this list to a Django template (in the file list_shapefiles.html), which is rendered into an HTML web page and returned back to the caller

    Let's go ahead and create the list_shapefiles.html template. Create a directory called templates within the editor directory, and create a new file in this directory named list_shapefiles.html. This file should have the following contents:
    
    .. code-block:: text
        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
        <body>
            <h1>ShapeEditor</h1>
        {% if shapefiles %}
            <b>Available Shapefiles:</b>
            <table border="0" cellspacing="0" cellpadding="5"
                   style="padding-left:20px">
          {% for shapefile in shapefiles %}
            <tr>
                <td>
                    <span style="font-family:monospace">{{ shapefile.filename
        }}</span>
            </td>
            <td>&nbsp;</td>
            <td>
                <a href="/editor/edit/{{ shapefile.id }}">
                    Edit
                </a>
            </td>
            <td>&nbsp;</td>
            <td>
                <a href="/editor/export/{{ shapefile.id }}">
                    Export
                </a>
            </td>
            <td>&nbsp;</td>
            <td>
                <a href="/editor/delete/{{ shapefile.id }}">
                    Delete
                </a>
            </td>
        </tr>
          {% endfor %}
            </table>
        {% endif %}
            <button type="button"
                onClick='window.location="/editor/import";'>
                Import New Shapefile
            </button>
          </body>
        </html>

    This template works as follows:

    - If the shapefiles list is not empty, it creates an HTML table to display the list of shapefiles
    - For each entry in the shapefiles list, a new row in the table is created
    - Each table row consists of the shapefile's filename (in monospaced text), along with Edit, Export, and Delete hyperlinks
    - Finally, an Import New Shapefile button is displayed at the bottom

    We'll look at the hyperlinks used in this template shortly, but for now just create
    the file, make sure the Django server is running, and reload your web browser.
    You should see the following page:

    .. image:: ./img/414-0.png
       :align: center

    As you can see, the shapefile we created earlier in the admin interface is
    shown, along with the relevant hyperlinks and buttons to access the rest
    of the ShapeEditor's functionality:

    - The **Edit** hyperlink will take the user to the /editor/edit/1 URL, which will let the user edit the shapefile with the given record ID
    - The **Export** hyperlink will take the user to the /exporter/export/1 URL, which will let the user download a copy of the shapefile from the server
    - The **Delete** hyperlink will take the user to the /editor/delete/1 URL, which will let the user delete the given shapefile
    - The **Import New Shapefile** button will take the user to the /importer/import URL, which will let the user upload a new shapefile to the server

    You can explore these URLs by clicking on them if you want—they won't do
    anything other than display an error page, but you can see how the URLs link the
    various parts of the ShapeEditor's functionality together. You can also take a detailed
    look at the Django error page, which can be quite helpful in tracking down bugs.

    Now that we have a working first page, let's start implementing the core
    functionality of the ShapeEditor application. We'll start with the logic required
    to import a shapefile.

.. tab:: 英文

    When the user first opens the ShapeEditor application, we want them to see a list
    of the previously-uploaded shapefiles, with "import", "edit", "export", and "delete"
    options. Let's build this list view, which acts as the starting point for the entire
    ShapeEditor application.

    This view is going to be part of the "editor" application, so we first need to create
    this application within our Django project. To do this, cd into the shapeEditor
    project directory and type the following::

        python manage.py startapp editor

    As usual, Django places the application in the top-level directory, making it
    a reusable application. We need to move it into our shapeEditor directory
    so that it becomes specific to our project. Either move the directory manually,
    or use the following terminal command::

        mv editor shapeEditor
    
    We now need to add our new application to the project. Edit the settings.py
    module, and add the following line to the end of the INSTALLED_APPS list::
    
        'shapeEditor.editor',

    Because the editor is going to support various URLs, we will want to give the editor
    its own URLConf module. To do this, create a new file in the shapeEditor/editor
    directory named urls.py, and enter the following into this file::

        # URLConf for the shapeEditor.editor application.
        
        from django.conf.urls import patterns, url
        
        urlpatterns = patterns('shapeEditor.editor.views',
            url(r'^$', 'list_shapefiles'),
        )

    This URLConf is going to handle all the URLs for the editor application. At present
    we have just one entry, that maps the top-level URL (defined using the r'^$'
    regular expression, which matches an empty string) to the list_shapefiles()
    view function.

    We next need to edit the top-level urls.py module so that the editor application's
    URLs will be included in the project. Change the top-level urls.py module (at
    shapeEditor/urls.py) to look like this::

        from django.conf.urls.defaults import patterns, include, url
        from django.contrib.gis import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            url(r'^editor/', include('shapeEditor.editor.urls')),
        )
        urlpatterns += patterns('',
            url(r'^admin/', include(admin.site.urls)),
        )

    Notice that we've now got two separate sets of URL patterns, one that places all
    of the shapeEditor.editor application's views into the editor URL, and another
    that places the django.contrib.gis.admin application's views into the admin
    URL. This is a very convenient way of splitting up a project's URLs, so that each
    application has its own section within the URL namespace.

    Now that we've set up our URL, let's write the view to go with it. We'll start by
    creating a very simple implementation of the list_shapefiles() view, just to
    make sure it works. Open the views.py module in the editor directory and
    change this file to look like this::

        from django.http import HttpResponse
        
        def list_shapefiles(request):
            return HttpResponse("in list_shapefiles")

    If it isn't already running, start up the GeoDjango web server. To do this,
    open a command-line window, cd into the geodjango project directory,
    and type the following::

        python manage.py runserver

    Then open your web browser and navigate to the following URL:
    
    http://127.0.0.1:8000/editor
    
    All going well, you should see in **list_shapefiles** appear on the web page. This tells
    you that you've successfully created the list_shapefiles() view function and
    have correctly set up the URLConf mappings to point to this view.

    We now want to create the view which will display the list of shapefiles. To do so,
    we'll make use of a Django template. Edit the views.py module again, and change
    this module's contents to look like this::

        from django.http import HttpResponse
        from django.shortcuts import render
        from shapeEditor.shared.models import Shapefile

        def list_shapefiles(request):
            shapefiles = Shapefile.objects.all().order_by("filename")
            return render(request, "list_shapefiles.html",
                          {'shapefiles' : shapefiles})

    The list_shapefiles() view function now does two things:

    - It loads the list of all Shapefile objects from the database into memory, sorted by filename
    - It passes this list to a Django template (in the file list_shapefiles.html), which is rendered into an HTML web page and returned back to the caller

    Let's go ahead and create the list_shapefiles.html template. Create a directory called templates within the editor directory, and create a new file in this directory named list_shapefiles.html. This file should have the following contents:
    
    .. code-block:: text
        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
        <body>
            <h1>ShapeEditor</h1>
        {% if shapefiles %}
            <b>Available Shapefiles:</b>
            <table border="0" cellspacing="0" cellpadding="5"
                   style="padding-left:20px">
          {% for shapefile in shapefiles %}
            <tr>
                <td>
                    <span style="font-family:monospace">{{ shapefile.filename
        }}</span>
            </td>
            <td>&nbsp;</td>
            <td>
                <a href="/editor/edit/{{ shapefile.id }}">
                    Edit
                </a>
            </td>
            <td>&nbsp;</td>
            <td>
                <a href="/editor/export/{{ shapefile.id }}">
                    Export
                </a>
            </td>
            <td>&nbsp;</td>
            <td>
                <a href="/editor/delete/{{ shapefile.id }}">
                    Delete
                </a>
            </td>
        </tr>
          {% endfor %}
            </table>
        {% endif %}
            <button type="button"
                onClick='window.location="/editor/import";'>
                Import New Shapefile
            </button>
          </body>
        </html>

    This template works as follows:

    - If the shapefiles list is not empty, it creates an HTML table to display the list of shapefiles
    - For each entry in the shapefiles list, a new row in the table is created
    - Each table row consists of the shapefile's filename (in monospaced text), along with Edit, Export, and Delete hyperlinks
    - Finally, an Import New Shapefile button is displayed at the bottom

    We'll look at the hyperlinks used in this template shortly, but for now just create
    the file, make sure the Django server is running, and reload your web browser.
    You should see the following page:

    .. image:: ./img/414-0.png
       :align: center

    As you can see, the shapefile we created earlier in the admin interface is
    shown, along with the relevant hyperlinks and buttons to access the rest
    of the ShapeEditor's functionality:

    - The **Edit** hyperlink will take the user to the /editor/edit/1 URL, which will let the user edit the shapefile with the given record ID
    - The **Export** hyperlink will take the user to the /exporter/export/1 URL, which will let the user download a copy of the shapefile from the server
    - The **Delete** hyperlink will take the user to the /editor/delete/1 URL, which will let the user delete the given shapefile
    - The **Import New Shapefile** button will take the user to the /importer/import URL, which will let the user upload a new shapefile to the server

    You can explore these URLs by clicking on them if you want—they won't do
    anything other than display an error page, but you can see how the URLs link the
    various parts of the ShapeEditor's functionality together. You can also take a detailed
    look at the Django error page, which can be quite helpful in tracking down bugs.
    
    Now that we have a working first page, let's start implementing the core
    functionality of the ShapeEditor application. We'll start with the logic required
    to import a shapefile.
