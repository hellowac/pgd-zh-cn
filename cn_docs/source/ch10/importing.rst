导入 Shapefile
============================================

Importing shapefiles

.. tab:: 中文

    The process of importing a shapefile involves the following steps:

    1. Displaying a form prompting the user to upload the shapefile's ZIP archive.
    2. Decompressing the ZIP file to extract the uploaded shapefile.
    3. Opening the shapefile and reading the data out of it into the database.
    4. Deleting the temporary files that we have created.

    Because of the complexity of this process, we'll use a separate application called
    shapefileIO to handle the behind-the-scenes logic of importing (and later, exporting)
    the shapefile's contents. This allows us to implement the user interface for importing
    shapefiles, without having to worry about these behind-the-scenes details.

    Let's start by creating the basic framework for the shapefileIO application. Using a
    terminal window, cd into the top-level directory and type the following::

        python manage.py startapp shapefileIO

    Then move the shapefileIO directory into the shapeEditor sub-directory, like this::
    
        mv shapefileIO shapeEditor

    While shapefileIO is a standard Django application, it won't have a user interface.
    Instead, it just defines various modules to be used by other parts of the system. For
    this reason, you can delete the views.py module from this application's directory.
    You can also delete the tests.py module, since we won't be defining any unit tests
    for this application.

    .. note::

        A Django application must have a models.py file and an __init__.py file. The models.py module can be empty if you don't define any database tables for the module, but it must exist or Django won't recognize the package as being an application. The application also needs to be listed in INSTALLED_APPS within the project's settings module.

    Next, we need to add the shapefileIO application to the project. Edit the settings.
    py module, and add the following line to the end of the INSTALLED_APPS list::
    
        'shapeEditor.shapefileIO',

    Within the shapefileIO directory, create a new module named importer.py,
    and enter the following into this file::

        def import_data(shapefile, character_encoding):
            return "More to come..."
    
    This function will attempt to import the contents of the compressed ZIP archive
    defined by the shapefile parameter, using the given character encoding. If the
    process fails, this function will return a suitable error message explaining what
    went wrong. If it succeeds, the import_data() function will return None.

    Now that we've defined the interface to our behind-the-scenes shapefile importer,
    we can start to implement the user-interface aspects of importing a shapefile.
    We'll start by defining a view function, and an associated Django form, to let the
    user import a shapefile.

.. tab:: 英文

    The process of importing a shapefile involves the following steps:

    1. Displaying a form prompting the user to upload the shapefile's ZIP archive.
    2. Decompressing the ZIP file to extract the uploaded shapefile.
    3. Opening the shapefile and reading the data out of it into the database.
    4. Deleting the temporary files that we have created.

    Because of the complexity of this process, we'll use a separate application called
    shapefileIO to handle the behind-the-scenes logic of importing (and later, exporting)
    the shapefile's contents. This allows us to implement the user interface for importing
    shapefiles, without having to worry about these behind-the-scenes details.

    Let's start by creating the basic framework for the shapefileIO application. Using a
    terminal window, cd into the top-level directory and type the following::

        python manage.py startapp shapefileIO

    Then move the shapefileIO directory into the shapeEditor sub-directory, like this::
    
        mv shapefileIO shapeEditor

    While shapefileIO is a standard Django application, it won't have a user interface.
    Instead, it just defines various modules to be used by other parts of the system. For
    this reason, you can delete the views.py module from this application's directory.
    You can also delete the tests.py module, since we won't be defining any unit tests
    for this application.

    .. note::

        A Django application must have a models.py file and an __init__.py file. The models.py module can be empty if you don't define any database tables for the module, but it must exist or Django won't recognize the package as being an application. The application also needs to be listed in INSTALLED_APPS within the project's settings module.

    Next, we need to add the shapefileIO application to the project. Edit the settings.
    py module, and add the following line to the end of the INSTALLED_APPS list::
    
        'shapeEditor.shapefileIO',

    Within the shapefileIO directory, create a new module named importer.py,
    and enter the following into this file::

        def import_data(shapefile, character_encoding):
            return "More to come..."
    
    This function will attempt to import the contents of the compressed ZIP archive
    defined by the shapefile parameter, using the given character encoding. If the
    process fails, this function will return a suitable error message explaining what
    went wrong. If it succeeds, the import_data() function will return None.
    
    Now that we've defined the interface to our behind-the-scenes shapefile importer,
    we can start to implement the user-interface aspects of importing a shapefile.
    We'll start by defining a view function, and an associated Django form, to let the
    user import a shapefile.

“导入 Shapefile”视图功能
-----------------------------------------
The "import shapefile" view function

.. tab:: 中文

    Let's start by creating a placeholder for this view. Edit the editor application's urls.
    py module and add a second entry to the shapeEditor.editor.views pattern list::

        urlpatterns = patterns('shapeEditor.editor.views',
            (r'^$', 'list_shapefiles'),
            (r'^import$', 'import_shapefile'),
        )

    Then edit the editor application's views.py module and add a dummy import_
    shapefile() view function to respond to this URL::

        def import_shapefile(request):
            return HttpResponse("More to come")

    You can test this if you want: run the Django server, go to the main page and click
    on the **Import New Shapefile** button. You should see the **More to come** message.

    To let the user enter data, we're going to use a Django form. **Forms** are custom
    classes that define the various fields, which will appear on the web page. In this
    case, our form will have two fields: one to accept the uploaded file, and other to
    select the character encoding from a pop-up menu. We're going to store this form
    in a file named forms.py in the editor directory; go ahead and create this file,
    and then edit it to look like this::

        from django import forms
            
        CHARACTER_ENCODINGS = [("ascii", "ASCII"),
                               ("latin1", "Latin-1"),
                               ("utf8", "UTF-8")]
            
        class ImportShapefileForm(forms.Form):
            import_file = forms.FileField(label="Select a Zipped Shapefile")
            character_encoding = forms.ChoiceField(choices=CHARACTER_ENCODINGS, initial="utf8")

    Our form will contain two fields. The first field is a FileField, which accepts
    uploaded files. We give this field a custom label which will be displayed in the
    web page. For the second field we'll use a ChoiceField, which displays a pop-up
    menu. Note that the CHARACTER_ENCODINGS list shows the various choices to display
    in the pop-up list; each entry in this list is a (value, label) tuple, where label is
    the string to be displayed and value is the actual value to be used for that field
    when the user chooses this item from the list.

    Now that we have created the form, go back to the editor application's views.py
    module, and replace the implementation of the import_shapefile() view function
    with the following::
        
        def import_shapefile(request):
            if request.method == "GET":
                form = ImportShapefileForm()
                return render(request, "import_shapefile.html",
                              {'form'   : form,
                              'err_msg' : None})

            elif request.method == "POST":
                form = ImportShapefileForm(request.POST,
                                           request.FILES)
                if form.is_valid():
                    shapefile = request.FILES['import_file']
                    encoding = request.POST['character_encoding']

                    err_msg = importer.import_shapefile(shapefile,
                                                        encoding)
                    if err_msg == None:
                        return HttpResponseRedirect("/shape-editor")
                else:
                    err_msg = None

                return render(request, "import_shapefile.html",
                              {'form': form,
                               'err_msg' : err_msg})

    Also, add the following import statements to the top of the module::

        from django.http import HttpResponseRedirect
        from shapeEditor.editor.forms import ImportShapefileForm
        from shapeEditor.shapefileIO import importer

    Let's take a look at what is happening here. The *import_shapefile()* function
    will initially be called with an HTTP GET request; this will cause the function to
    create a new *ImportShapefileForm* object, and then call the *render()* function
    to display that form to the user. When the form is submitted, the *import_shapefile()* function will be called with an HTTP POST request. In this case, the
    *ImportShapefileForm* will be created with the submitted data (request.POST and
    *request.FILES*), and the form will be checked to see that the entered data is valid.
    If so, we extract the uploaded shapefile and the selected character encoding.

    We then ask the shapefile importer to import the shapefile's data. This will return
    an error message if something goes wrong. If there is no error, we redirect the user
    back to the main */editor* page so that the newly-imported shapefile can be shown.

    If the form was not valid, or if the import process failed for some reason, we once
    again call the *render()* function to display the form to the user, this time with an
    appropriate error message. Note that Django will automatically display an error
    message if there is a problem with the form.

    To display the form to the user, we'll use a Django template and pass the form
    object as a parameter. Let's create that template now; add a new file named
    import_shapefile.html in the editor application's templates directory
    and enter the following text into this file:

    .. code-block:: text

        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
        <body>
            <h1>Import Shapefile</h1>
        {% if err_msg %}
            <b><i>{{ err_msg }}</i></b>
        {% endif %}
            <form enctype="multipart/form-data" method="post"
                  action="import">
                {{ form.as_p }}
                <input type="submit" value="Submit"/>
                <button type="button"
                        onClick='window.location="/editor";'>
                Cancel
            </button>
            </form>
        </body>
        </html>

    As you can see, this template defines an HTML <form> and adds **Submit** and **Cancel**
    buttons. The body of the form is not specified. Instead, we use {{ form.as_p }} to
    render the form object as a series of <p> (paragraph) elements. Near the top of the
    page, we also display the error message if there is one.

    Let's test this out. Start up the Django web server if it is not already running,
    open a web browser and navigate to the http://127.0.0.1:8000/editor URL.
    Then click on the **Import New Shapefile** button. All going well, you should see
    the following page:

    .. image:: ./img/419-0.png
       :align: center

    If you attempt to submit the form without uploading anything, an error message
    will appear saying that the import_file field is required. This is the default
    error-handling for any form; by default, all fields are required. If you do select
    a file for uploading, the importer will return the string **More to come...**, so this
    message should appear near the top of the page.

    Now that we've implemented the form itself, let's return to our shapefileIO
    application and implement the logic needed to process the uploaded shapefile.

.. tab:: 英文

    Let's start by creating a placeholder for this view. Edit the editor application's urls.
    py module and add a second entry to the shapeEditor.editor.views pattern list::

        urlpatterns = patterns('shapeEditor.editor.views',
            (r'^$', 'list_shapefiles'),
            (r'^import$', 'import_shapefile'),
        )

    Then edit the editor application's views.py module and add a dummy import_
    shapefile() view function to respond to this URL::

        def import_shapefile(request):
            return HttpResponse("More to come")

    You can test this if you want: run the Django server, go to the main page and click
    on the **Import New Shapefile** button. You should see the **More to come** message.

    To let the user enter data, we're going to use a Django form. **Forms** are custom
    classes that define the various fields, which will appear on the web page. In this
    case, our form will have two fields: one to accept the uploaded file, and other to
    select the character encoding from a pop-up menu. We're going to store this form
    in a file named forms.py in the editor directory; go ahead and create this file,
    and then edit it to look like this::

        from django import forms
            
        CHARACTER_ENCODINGS = [("ascii", "ASCII"),
                               ("latin1", "Latin-1"),
                               ("utf8", "UTF-8")]
            
        class ImportShapefileForm(forms.Form):
            import_file = forms.FileField(label="Select a Zipped Shapefile")
            character_encoding = forms.ChoiceField(choices=CHARACTER_ENCODINGS, initial="utf8")

    Our form will contain two fields. The first field is a FileField, which accepts
    uploaded files. We give this field a custom label which will be displayed in the
    web page. For the second field we'll use a ChoiceField, which displays a pop-up
    menu. Note that the CHARACTER_ENCODINGS list shows the various choices to display
    in the pop-up list; each entry in this list is a (value, label) tuple, where label is
    the string to be displayed and value is the actual value to be used for that field
    when the user chooses this item from the list.

    Now that we have created the form, go back to the editor application's views.py
    module, and replace the implementation of the import_shapefile() view function
    with the following::
        
        def import_shapefile(request):
            if request.method == "GET":
                form = ImportShapefileForm()
                return render(request, "import_shapefile.html",
                              {'form'   : form,
                              'err_msg' : None})

            elif request.method == "POST":
                form = ImportShapefileForm(request.POST,
                                           request.FILES)
                if form.is_valid():
                    shapefile = request.FILES['import_file']
                    encoding = request.POST['character_encoding']

                    err_msg = importer.import_shapefile(shapefile,
                                                        encoding)
                    if err_msg == None:
                        return HttpResponseRedirect("/shape-editor")
                else:
                    err_msg = None

                return render(request, "import_shapefile.html",
                              {'form': form,
                               'err_msg' : err_msg})

    Also, add the following import statements to the top of the module::

        from django.http import HttpResponseRedirect
        from shapeEditor.editor.forms import ImportShapefileForm
        from shapeEditor.shapefileIO import importer

    Let's take a look at what is happening here. The *import_shapefile()* function
    will initially be called with an HTTP GET request; this will cause the function to
    create a new *ImportShapefileForm* object, and then call the *render()* function
    to display that form to the user. When the form is submitted, the *import_shapefile()* function will be called with an HTTP POST request. In this case, the
    *ImportShapefileForm* will be created with the submitted data (request.POST and
    *request.FILES*), and the form will be checked to see that the entered data is valid.
    If so, we extract the uploaded shapefile and the selected character encoding.

    We then ask the shapefile importer to import the shapefile's data. This will return
    an error message if something goes wrong. If there is no error, we redirect the user
    back to the main */editor* page so that the newly-imported shapefile can be shown.

    If the form was not valid, or if the import process failed for some reason, we once
    again call the *render()* function to display the form to the user, this time with an
    appropriate error message. Note that Django will automatically display an error
    message if there is a problem with the form.

    To display the form to the user, we'll use a Django template and pass the form
    object as a parameter. Let's create that template now; add a new file named
    import_shapefile.html in the editor application's templates directory
    and enter the following text into this file:

    .. code-block:: text

        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
        <body>
            <h1>Import Shapefile</h1>
        {% if err_msg %}
            <b><i>{{ err_msg }}</i></b>
        {% endif %}
            <form enctype="multipart/form-data" method="post"
                  action="import">
                {{ form.as_p }}
                <input type="submit" value="Submit"/>
                <button type="button"
                        onClick='window.location="/editor";'>
                Cancel
            </button>
            </form>
        </body>
        </html>

    As you can see, this template defines an HTML <form> and adds **Submit** and **Cancel**
    buttons. The body of the form is not specified. Instead, we use {{ form.as_p }} to
    render the form object as a series of <p> (paragraph) elements. Near the top of the
    page, we also display the error message if there is one.

    Let's test this out. Start up the Django web server if it is not already running,
    open a web browser and navigate to the http://127.0.0.1:8000/editor URL.
    Then click on the **Import New Shapefile** button. All going well, you should see
    the following page:

    .. image:: ./img/419-0.png
       :align: center

    If you attempt to submit the form without uploading anything, an error message
    will appear saying that the import_file field is required. This is the default
    error-handling for any form; by default, all fields are required. If you do select
    a file for uploading, the importer will return the string **More to come...**, so this
    message should appear near the top of the page.

    Now that we've implemented the form itself, let's return to our shapefileIO
    application and implement the logic needed to process the uploaded shapefile.


提取已上传的 Shapefile
-----------------------------------------
Extracting the uploaded shapefile

.. tab:: 中文

    It is now time for us to write the body of our import_data() function. Go back
    to the importer.py module within the shapefileIO application, and delete the
    dummy return statement we added earlier.

    When we use a form that includes a FileField, Django returns to us an
    UploadedFile object representing the uploaded file. Our first task is to read the
    contents of the UploadedFile object and store it in a temporary file on disk so
    that we can work with it. Add the following lines to your import_data() function::

        fd,fname = tempfile.mkstemp(suffix=".zip")
        os.close(fd)

        f = open(fname, "wb")
        for chunk in shapefile.chunks():
            f.write(chunk)
        f.close()

    As you can see, we use the tempfile module from the Python standard library to
    create a temporary file, and then copy the contents of the shapefile object into it.

    Because tempfile.mkstemp() returns both a file descriptor and a filename, we call
    os.close(fd) to close the file descriptor. This allows us to reopen the file using
    open() and write to it in the normal way.

    We're now ready to open the temporary file and check that it is indeed a ZIP archive
    containing the files which make up a shapefile. Here is how we can do this::

        if not zipfile.is_zipfile(fname):
            os.remove(fname)
            return "Not a valid zip archive."

        zip = zipfile.ZipFile(fname)

        required_suffixes = [".shp", ".shx", ".dbf", ".prj"]
        has_suffix = {}
        for suffix in required_suffixes:
            has_suffix[suffix] = False

        for info in zip.infolist():
            extension = os.path.splitext(info.filename)[1].lower()
            if extension in required_suffixes:
                has_suffix[extension] = True

        for suffix in required_suffixes:
            if not has_suffix[suffix]:
                zip.close()
                os.remove(fname)
                return "Archive missing required "+suffix+" file."

    Note that we use the Python standard library's zipfile module to check the
    contents of the uploaded ZIP archive, and return a suitable error message
    if something is wrong. We also delete the temporary file before returning
    an error message, so that we don't leave temporary files lying around.

    Finally, now that we know that the uploaded file is a valid ZIP archive containing
    the files that make up a shapefile, we can extract these files and store them into a
    temporary directory::

        shapefile_name = None
        dst_dir = tempfile.mkdtemp()
        for info in zip.infolist():
            if info.filename.endswith(".shp"):
                shapefile_name = info.filename

            dst_file = os.path.join(dst_dir, info.filename)
            f = open(dst_file, "wb")
            f.write(zip.read(info.filename))
            f.close()
        zip.close()

    Note that we create a temporary directory to hold the extracted files before copying
    the files into this directory. At the same time, we remember the name of the main
    .shp file from the archive, as we'll need to use this name when we open the
    shapefile.

    Because we've used some of the Python standard library modules in this code,
    you'll also need to add the following to the top of the module::
    
        import os, os.path, tempfile, zipfile

.. tab:: 英文

    It is now time for us to write the body of our import_data() function. Go back
    to the importer.py module within the shapefileIO application, and delete the
    dummy return statement we added earlier.

    When we use a form that includes a FileField, Django returns to us an
    UploadedFile object representing the uploaded file. Our first task is to read the
    contents of the UploadedFile object and store it in a temporary file on disk so
    that we can work with it. Add the following lines to your import_data() function::

        fd,fname = tempfile.mkstemp(suffix=".zip")
        os.close(fd)

        f = open(fname, "wb")
        for chunk in shapefile.chunks():
            f.write(chunk)
        f.close()

    As you can see, we use the tempfile module from the Python standard library to
    create a temporary file, and then copy the contents of the shapefile object into it.

    Because tempfile.mkstemp() returns both a file descriptor and a filename, we call
    os.close(fd) to close the file descriptor. This allows us to reopen the file using
    open() and write to it in the normal way.

    We're now ready to open the temporary file and check that it is indeed a ZIP archive
    containing the files which make up a shapefile. Here is how we can do this::

        if not zipfile.is_zipfile(fname):
            os.remove(fname)
            return "Not a valid zip archive."

        zip = zipfile.ZipFile(fname)

        required_suffixes = [".shp", ".shx", ".dbf", ".prj"]
        has_suffix = {}
        for suffix in required_suffixes:
            has_suffix[suffix] = False

        for info in zip.infolist():
            extension = os.path.splitext(info.filename)[1].lower()
            if extension in required_suffixes:
                has_suffix[extension] = True

        for suffix in required_suffixes:
            if not has_suffix[suffix]:
                zip.close()
                os.remove(fname)
                return "Archive missing required "+suffix+" file."

    Note that we use the Python standard library's zipfile module to check the
    contents of the uploaded ZIP archive, and return a suitable error message
    if something is wrong. We also delete the temporary file before returning
    an error message, so that we don't leave temporary files lying around.

    Finally, now that we know that the uploaded file is a valid ZIP archive containing
    the files that make up a shapefile, we can extract these files and store them into a
    temporary directory::

        shapefile_name = None
        dst_dir = tempfile.mkdtemp()
        for info in zip.infolist():
            if info.filename.endswith(".shp"):
                shapefile_name = info.filename

            dst_file = os.path.join(dst_dir, info.filename)
            f = open(dst_file, "wb")
            f.write(zip.read(info.filename))
            f.close()
        zip.close()

    Note that we create a temporary directory to hold the extracted files before copying
    the files into this directory. At the same time, we remember the name of the main
    .shp file from the archive, as we'll need to use this name when we open the
    shapefile.

    Because we've used some of the Python standard library modules in this code,
    you'll also need to add the following to the top of the module::
    
        import os, os.path, tempfile, zipfile


导入 Shapefile 的内容
-----------------------------------------
Importing the shapefile's contents

.. tab:: 中文

    Now that we've extracted the shapefile's files out of the ZIP archive, we are ready to
    import the data from the uploaded shapefile. The process of importing the shapefile's
    contents involves the following steps:

    1. Opening the shapefile.
    2. Adding the Shapefile object to the database.
    3. Defining the shapefile's attributes.
    4. Storing the shapefile's features.
    5. Storing the shapefile's attributes.

    Let's work through these steps one at a time.

.. tab:: 英文

    Now that we've extracted the shapefile's files out of the ZIP archive, we are ready to
    import the data from the uploaded shapefile. The process of importing the shapefile's
    contents involves the following steps:

    1. Opening the shapefile.
    2. Adding the Shapefile object to the database.
    3. Defining the shapefile's attributes.
    4. Storing the shapefile's features.
    5. Storing the shapefile's attributes.

    Let's work through these steps one at a time.


打开 Shapefile
~~~~~~~~~~~~~~~~~~
Open the shapefile

.. tab:: 中文

    We will use the OGR library to open the shapefile::

        try:
            datasource  = ogr.Open(os.path.join(dst_dir,
                                                shapefileName))
            layer = datasource.GetLayer(0)
            shapefileOK = True
        except:
            traceback.print_exc()
            shapefileOK = False

        if not shapefileOK:
            os.remove(fname)
            shutil.rmtree(dst_dir)
            return "Not a valid shapefile."

    Once again, if something goes wrong we clean up our temporary files and return a
    suitable error message. We're also using the traceback library module to display
    debugging information in the web server's log, while returning a friendly error
    message that will be shown to the user.

    .. note::

        In this program, we will be using OGR directly to read and write shapefiles. GeoDjango provides its own Python interface to OGR in the contrib.gis.gdal package, but unfortunately GeoDjango's version doesn't implement writing to shapefiles. Because of this, we will use the OGR Python bindings directly, and require you to install OGR separately.
    
    Because this code uses a couple of standard library modules, as well as the OGR library, we'll have to add the following import statements to the top of the importer.py module::

        import shutil, traceback
        from osgeo import ogr

.. tab:: 英文

    We will use the OGR library to open the shapefile::

        try:
            datasource  = ogr.Open(os.path.join(dst_dir,
                                                shapefileName))
            layer = datasource.GetLayer(0)
            shapefileOK = True
        except:
            traceback.print_exc()
            shapefileOK = False

        if not shapefileOK:
            os.remove(fname)
            shutil.rmtree(dst_dir)
            return "Not a valid shapefile."

    Once again, if something goes wrong we clean up our temporary files and return a
    suitable error message. We're also using the traceback library module to display
    debugging information in the web server's log, while returning a friendly error
    message that will be shown to the user.

    .. note::

        In this program, we will be using OGR directly to read and write shapefiles. GeoDjango provides its own Python interface to OGR in the contrib.gis.gdal package, but unfortunately GeoDjango's version doesn't implement writing to shapefiles. Because of this, we will use the OGR Python bindings directly, and require you to install OGR separately.
    
    Because this code uses a couple of standard library modules, as well as the OGR library, we'll have to add the following import statements to the top of the importer.py module::

        import shutil, traceback
        from osgeo import ogr


将 Shapefile 对象添加到数据库
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Add the Shapefile object to the database

.. tab:: 中文

    Now that we've successfully opened the shapefile, we are ready to read the data out
    of it. First off, we'll create the Shapefile object to represent this imported shapefile::

        src_spatial_ref = layer.GetSpatialRef()
        shapefile = Shapefile(filename=shapefile_name,
                              srs_wkt=src_spatial_ref.ExportToWkt(),
                              geom_type="...",
                              encoding=character_encoding)
        shapefile.save()

    As you can see, we get the spatial reference from the shapefile's layer, and then store
    the shapefile's name, spatial reference, and encoding into a Shapefile object, which
    we then save into the database. There's only one glitch: what value are we going to
    store into the geom_type field?

    The geom_type field is supposed to hold the name of the geometry type that this
    shapefile holds. While the OGR shapefile is able to tell us the geometry type as a
    numeric constant, the OGRGeometryTypeToName() function in OGR is not exposed by
    the Python bindings, so we can't get the name of the geometry directly using OGR.

    To work around this, we'll implement our own version of
    OGRGeometryTypeToName(). Because we're going to have a several of these
    functions, we'll store this in a separate module, which we'll call utils.py. Go into
    the shared application directory and create a new file called utils.py. Edit this file,
    and add the following to it::

        from osgeo import ogr

        def ogr_type_to_geometry_mname(ogr_type):
        return {ogr.wkbUnknown: 'Unknown',
                ogr.wkbPoint: 'Point',
                ogr.wkbLineString: 'LineString',
                ogr.wkbPolygon: 'Polygon',
                ogr.wkbMultiPoint: 'MultiPoint',
                ogr.wkbMultiLineString: 'MultiLineString',
                ogr.wkbMultiPolygon: 'MultiPolygon',
                ogr.wkbGeometryCollection : 'GeometryCollection',
                ogr.wkbNone: 'None',
                ogr.wkbLinearRing : 'LinearRing'}.get(ogr_type)

    .. note::

        Every self-respecting Python program should have a utils.py module; it's about time we added one in the ShapeEditor.

    Now that we have our own version of OGRGeometryTypeToName(), we can use this
    to set the geom_type field in the Shapefile object. Go back to the importer.py
    module and make the following changes to the end of your import_data() function::

        src_spatial_ref = layer.GetSpatialRef()

        geometry_type = layer.GetLayerDefn().GetGeomType()
        geometry_name = \
                utils.ogr_type_to_geometry_name(geometry_type)

        shapefile = Shapefile(filename=shapefileName,
                              srs_wkt= src_spatial_ref.ExportToWkt(),
                              geom_type=geometry_name,
                              encoding=character_encoding)
        shapefile.save()

    To make this code work, we'll have to add the following import statements to the
    top of the importer.py module::

        from shapeEditor.shared.models import Shapefile
        from shapeEditor.shared import utils

.. tab:: 英文

    Now that we've successfully opened the shapefile, we are ready to read the data out
    of it. First off, we'll create the Shapefile object to represent this imported shapefile::

        src_spatial_ref = layer.GetSpatialRef()
        shapefile = Shapefile(filename=shapefile_name,
                              srs_wkt=src_spatial_ref.ExportToWkt(),
                              geom_type="...",
                              encoding=character_encoding)
        shapefile.save()

    As you can see, we get the spatial reference from the shapefile's layer, and then store
    the shapefile's name, spatial reference, and encoding into a Shapefile object, which
    we then save into the database. There's only one glitch: what value are we going to
    store into the geom_type field?

    The geom_type field is supposed to hold the name of the geometry type that this
    shapefile holds. While the OGR shapefile is able to tell us the geometry type as a
    numeric constant, the OGRGeometryTypeToName() function in OGR is not exposed by
    the Python bindings, so we can't get the name of the geometry directly using OGR.

    To work around this, we'll implement our own version of
    OGRGeometryTypeToName(). Because we're going to have a several of these
    functions, we'll store this in a separate module, which we'll call utils.py. Go into
    the shared application directory and create a new file called utils.py. Edit this file,
    and add the following to it::

        from osgeo import ogr

        def ogr_type_to_geometry_mname(ogr_type):
        return {ogr.wkbUnknown: 'Unknown',
                ogr.wkbPoint: 'Point',
                ogr.wkbLineString: 'LineString',
                ogr.wkbPolygon: 'Polygon',
                ogr.wkbMultiPoint: 'MultiPoint',
                ogr.wkbMultiLineString: 'MultiLineString',
                ogr.wkbMultiPolygon: 'MultiPolygon',
                ogr.wkbGeometryCollection : 'GeometryCollection',
                ogr.wkbNone: 'None',
                ogr.wkbLinearRing : 'LinearRing'}.get(ogr_type)

    .. note::

        Every self-respecting Python program should have a utils.py module; it's about time we added one in the ShapeEditor.

    Now that we have our own version of OGRGeometryTypeToName(), we can use this
    to set the geom_type field in the Shapefile object. Go back to the importer.py
    module and make the following changes to the end of your import_data() function::

        src_spatial_ref = layer.GetSpatialRef()

        geometry_type = layer.GetLayerDefn().GetGeomType()
        geometry_name = \
                utils.ogr_type_to_geometry_name(geometry_type)

        shapefile = Shapefile(filename=shapefileName,
                              srs_wkt= src_spatial_ref.ExportToWkt(),
                              geom_type=geometry_name,
                              encoding=character_encoding)
        shapefile.save()

    To make this code work, we'll have to add the following import statements to the
    top of the importer.py module::

        from shapeEditor.shared.models import Shapefile
        from shapeEditor.shared import utils


定义 Shapefile 的属性
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Define the shapefile's attributes

.. tab:: 中文

    Now that we've created the Shapefile object to represent the imported shapefile,
    our next task is to create Attribute objects describing the shapefile's attributes. We
    can do this by querying the OGR shapefile; add the following code to the end of the
    import_data() function::

        attributes = []
        layer_def = layer.GetLayerDefn()
        for i in range(layer_def.GetFieldCount()):
            field_def = layer_def.GetFieldDefn(i)
            attr = Attribute(shapefile=shapefile,
                             name=field_def.GetName(),
                             type=field_def.GetType(),
                             width=field_def.GetWidth(),
                             precision=field_def.GetPrecision())
            attr.save()
            attributes.append(attr)

    Note that, as well as saving the Attribute objects into a database, we also create a
    separate list of these attributes in a variable named attributes. We'll use this later
    on, when we import the attribute values for each feature.

    Don't forget to add the following import statement to the top of the module::

        from geodjango.shapeEditor.models import Attribute

.. tab:: 英文

    Now that we've created the Shapefile object to represent the imported shapefile,
    our next task is to create Attribute objects describing the shapefile's attributes. We
    can do this by querying the OGR shapefile; add the following code to the end of the
    import_data() function::

        attributes = []
        layer_def = layer.GetLayerDefn()
        for i in range(layer_def.GetFieldCount()):
            field_def = layer_def.GetFieldDefn(i)
            attr = Attribute(shapefile=shapefile,
                             name=field_def.GetName(),
                             type=field_def.GetType(),
                             width=field_def.GetWidth(),
                             precision=field_def.GetPrecision())
            attr.save()
            attributes.append(attr)

    Note that, as well as saving the Attribute objects into a database, we also create a
    separate list of these attributes in a variable named attributes. We'll use this later
    on, when we import the attribute values for each feature.

    Don't forget to add the following import statement to the top of the module::

        from geodjango.shapeEditor.models import Attribute


存储 Shapefile 的功能
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Store the shapefile's features

.. tab:: 中文

    Our next task is to extract the shapefile's features and store them as Feature objects
    in the database. Because the shapefile's features can be in any spatial reference,
    we need to transform them into our internal spatial reference system (EPSG 4326,
    unprojected latitude, and longitude values) before we can store them. To do this,
    we'll use an OGR CoordinateTransformation() object.

    Here is how we're going to scan through the shapefile's features, extract the geometry
    from each feature, transform it into the EPSG 4326 spatial reference, and convert it into
    a GeoDjango GEOS geometry object so that we can store it into the database::

    dst_spatial_ref = osr.SpatialReference()
    dst_spatial_ref.ImportFromEPSG(4326)

    coord_transform = osr.CoordinateTransformation(src_spatial_ref,
                                                   dst_spatial_ref)

    for i in range(layer.GetFeatureCount()):
        src_feature = layer.GetFeature(i)
        src_geometry = src_feature.GetGeometryRef()
        src_geometry.Transform(coord_transform)
        geometry = GEOSGeometry(src_geometry.ExportToWkt())

    So far so good; we now have a GEOS geometry object which we can store into the
    Feature object. Unfortunately, we are now faced with a couple of problems. First,
    the inability of Shapefiles to distinguish between Polygons and MultiPolygons (and
    between LineStrings and MultiLineStrings) as described in the previous chapter means
    that we have to wrap a Polygon geometry inside a MultiPolygon, and a LineString
    geometry inside a MultiLineString, so that all the features in the shapefile will have the
    same geometry type. This is kind of messy, so we'll write a utils.py function to do
    this. Add the following line to the end of your import_data() function (along with
    the code above, if you haven't already typed this in) to wrap the geometry::

        geometry = utils.wrap_geos_geometry(geometry)

    The second problem we have is that we need to decide which particular field within
    the Feature object will hold our geometry. When we defined the Feature object, we
    had to create separate geometry fields for each of the geometry types; we now need
    to decide which of these fields will be used to store a given type of geometry.

    Because we sometimes have to wrap up geometries, we can't simply use the
    geometry name to identify the field. This is another messy function that we'll
    implement in utils.py. For now, just add the following line to the end of your
    import_data() function::

        geometry_field = utils.calc_geometry_field(geometry_name)

    Now that we've sorted out these problems, we're finally ready to store the feature's
    geometry into a Feature object within the database::

        args = {}
        args['shapefile'] = shapefile
        args[geometry_field] = geometry
        feature = Feature(**args)
        feature.save()

    Note that we use keyword arguments (**args) to create the Feature object. This lets
    us store the geometry into the correct field of the Feature object with a minimum
    of fuss. The alternative, using a series of if...elif...elif statements would have
    been much more tedious.

    Before we move on, we'd better implement those two extra functions in the utils.
    py module. Here is the implementation for the wrap_geos_geometry() function::

        def wrap_geos_Geometry(geometry):
            if geometry.geom_type == "Polygon":
                return MultiPolygon(geometry)
            elif geometry.geom_type == "LineString":
                return MultiLineString(geometry)
            else:
                return geometry

    Here is the implementation for the calc_geometry_field() function::

        def calc_geometry_field(geometry_type):
            if geometry_type == "Polygon":
                return "geom_multipolygon"
            elif geometry_type == "LineString":
                return "geom_multilinestring"
            else:
                return "geom_" + geometry_type.lower()

    You're also going to have to add the following import statement to the top
    of the utils.py module::

        from django.contrib.gis.geos.collections \
            import MultiPolygon, MultiLineString

    Finally, in the importer.py module, you'll have to add the following
    import statements::

        from django.contrib.gis.geos.geometry import GEOSGeometry
        from osgeo import osr
        from geodjango.shapeEditor.models import Feature

.. tab:: 英文

    Our next task is to extract the shapefile's features and store them as Feature objects
    in the database. Because the shapefile's features can be in any spatial reference,
    we need to transform them into our internal spatial reference system (EPSG 4326,
    unprojected latitude, and longitude values) before we can store them. To do this,
    we'll use an OGR CoordinateTransformation() object.

    Here is how we're going to scan through the shapefile's features, extract the geometry
    from each feature, transform it into the EPSG 4326 spatial reference, and convert it into
    a GeoDjango GEOS geometry object so that we can store it into the database::

    dst_spatial_ref = osr.SpatialReference()
    dst_spatial_ref.ImportFromEPSG(4326)

    coord_transform = osr.CoordinateTransformation(src_spatial_ref,
                                                   dst_spatial_ref)

    for i in range(layer.GetFeatureCount()):
        src_feature = layer.GetFeature(i)
        src_geometry = src_feature.GetGeometryRef()
        src_geometry.Transform(coord_transform)
        geometry = GEOSGeometry(src_geometry.ExportToWkt())

    So far so good; we now have a GEOS geometry object which we can store into the
    Feature object. Unfortunately, we are now faced with a couple of problems. First,
    the inability of Shapefiles to distinguish between Polygons and MultiPolygons (and
    between LineStrings and MultiLineStrings) as described in the previous chapter means
    that we have to wrap a Polygon geometry inside a MultiPolygon, and a LineString
    geometry inside a MultiLineString, so that all the features in the shapefile will have the
    same geometry type. This is kind of messy, so we'll write a utils.py function to do
    this. Add the following line to the end of your import_data() function (along with
    the code above, if you haven't already typed this in) to wrap the geometry::

        geometry = utils.wrap_geos_geometry(geometry)

    The second problem we have is that we need to decide which particular field within
    the Feature object will hold our geometry. When we defined the Feature object, we
    had to create separate geometry fields for each of the geometry types; we now need
    to decide which of these fields will be used to store a given type of geometry.

    Because we sometimes have to wrap up geometries, we can't simply use the
    geometry name to identify the field. This is another messy function that we'll
    implement in utils.py. For now, just add the following line to the end of your
    import_data() function::

        geometry_field = utils.calc_geometry_field(geometry_name)

    Now that we've sorted out these problems, we're finally ready to store the feature's
    geometry into a Feature object within the database::

        args = {}
        args['shapefile'] = shapefile
        args[geometry_field] = geometry
        feature = Feature(**args)
        feature.save()

    Note that we use keyword arguments (**args) to create the Feature object. This lets
    us store the geometry into the correct field of the Feature object with a minimum
    of fuss. The alternative, using a series of if...elif...elif statements would have
    been much more tedious.

    Before we move on, we'd better implement those two extra functions in the utils.
    py module. Here is the implementation for the wrap_geos_geometry() function::

        def wrap_geos_Geometry(geometry):
            if geometry.geom_type == "Polygon":
                return MultiPolygon(geometry)
            elif geometry.geom_type == "LineString":
                return MultiLineString(geometry)
            else:
                return geometry

    Here is the implementation for the calc_geometry_field() function::

        def calc_geometry_field(geometry_type):
            if geometry_type == "Polygon":
                return "geom_multipolygon"
            elif geometry_type == "LineString":
                return "geom_multilinestring"
            else:
                return "geom_" + geometry_type.lower()

    You're also going to have to add the following import statement to the top
    of the utils.py module::

        from django.contrib.gis.geos.collections \
            import MultiPolygon, MultiLineString

    Finally, in the importer.py module, you'll have to add the following
    import statements::

        from django.contrib.gis.geos.geometry import GEOSGeometry
        from osgeo import osr
        from geodjango.shapeEditor.models import Feature


存储 Shapefile 的属性
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Store the shapefile's attributes

.. tab:: 中文

    Now that we've dealt with the feature's geometry, we can now look at importing the
    feature's attributes. The basic process involves iterating over the attributes, extracting
    the attribute value from the OGR feature, creating an AttributeValue object to store
    the value, and then saving it into the database::

        for attr in attributes:
            value = ...
            attr_value = AttributeValue(feature=feature,
                                        attribute=attr,
                                        value=value)
            attr_value.save()

    The challenge is to extract the attribute value from the feature. Because the OGR
    Feature object has different methods to extract different types of field values,
    we are going to have to check for the different field types, call the appropriate
    GetFieldAs() method, convert the resulting value to a string, and then store this
    string into the AttributeValue object. NULL values will also have to be handled
    appropriately. In addition, we have to deal with character encoding; any string
    values will have to be converted from the shapefile's character encoding into
    Unicode text so that they can be saved into the database. Because of this complexity,
    we'll define a new utils.py function to do the hard work, and simply call that
    function from import_data().

    Note that, because the user might have selected the wrong character encoding for
    the shapefile, the process of extracting the attribute value can actually fail. Because
    of this, we have to add error-handling to our code. To support error-handling, our
    utility function, get_ogr_feature_attribute(), will return a (success, result)
    tuple, where success will be true if and only if the attribute was successfully
    extracted, and result will either be the extracted attribute value (as a string),
    or an error message explaining why the operation failed.

    Let's add the necessary code to our import_data() function to store the
    attribute values into the database and gracefully handle any conversion
    errors that might occur::

        for attr in attributes:
            success,result = utils.getOGRFeatureAttribute(
                                                            attr, srcFeature,
                                                            character_encoding)
            if not success:
                os.remove(fname)
                shutil.rmtree(dst_dir)
                shapefile.delete()
                return result

            attr_value = AttributeValue(feature=feature,
                                        attribute=attr,
                                        value=result)
            attr_value.save()

    Note that we pass the Attribute object, the OGR feature, and the character encoding
    to the get_ogr_feature_attribute() function. If an error occurs, we clean up the
    temporary files, delete the shapefile we created earlier, and return the error message
    back to the caller. If the attribute was successfully extracted, we create a new
    AttributeValue object with the attribute's value, and save it into the database.

    .. note::

        Note that we use shapefile.delete() to remove the partially-imported shapefile from the database. By default, Django will also automatically delete any records that are related to the record being deleted through a ForeignKey field. This means that the Shapefile object will be deleted, along with all the related Attribute, Feature, and AttributeValue objects. With one line of code, we can completely remove all references to the shapefile's data.

    Now let's implement that get_ogr_feature_attribute() function. Add the following to utils.py::

        def getOGRFeatureAttribute(attr, feature, encoding):
            attr_name = str(attr.name)

            if not feature.IsFieldSet(attr_name):
                return (True, None)

            needs_encoding = False
            if attr.type == ogr.OFTInteger:
                value = str(feature.GetFieldAsInteger(attr_name))
            elif attr.type == ogr.OFTIntegerList:
                value = repr(feature.GetFieldAsIntegerList(attr_name))
            elif attr.type == ogr.OFTReal:
                value = feature.GetFieldAsDouble(attr_name)
                value = "%*.*f" % (attr.width, attr.precision, value)
            elif attr.type == ogr.OFTRealList:
                values = feature.GetFieldAsDoubleList(attr_name)
                str_values = []
                for value in values:
                    str_values.append("%*.*f" % (attr.width,
                                                attr.precision,
                                                value))
                value = repr(str_Values)
            elif attr.type == ogr.OFTString:
                value = feature.GetFieldAsString(attr_name)
                needs_encoding = True
            elif attr.type == ogr.OFTStringList:
                value = repr(feature.GetFieldAsStringList(attr_name))
                needs_encoding = True
            elif attr.type == ogr.OFTDate:
                parts = feature.GetFieldAsDateTime(attr_name)
                year,month,day,hour,minute,second,tzone = parts
                value = "%d,%d,%d,%d" % (year,month,day,tzone)
            elif attr.type == ogr.OFTTime:
                parts = feature.GetFieldAsDateTime(attr_name)
                year,month,day,hour,minute,second,tzone = parts
                value = "%d,%d,%d,%d" % (hour,minute,second,tzone)
            elif attr.type == ogr.OFTDateTime:
                parts = feature.GetFieldAsDateTime(attr_name)
                year,month,day,hour,minute,second,tzone = parts
                value = "%d,%d,%d,%d,%d,%d,%d,%d" % (year,month,day,
                                                     hour,minute,
                                                     second,tzone)
            else:
                return (False, "Unsupported attribute type: " +
                                str(attr.type))
            if needs_encoding:
                try:
                    value = value.decode(encoding)
                except UnicodeDecodeError:
                    return (False, "Unable to decode value in " +
                            repr(attr_name) + " attribute.&nbsp; " +
                            "Are you sure you're using the right " +
                            "character encoding?")
            return (True, value)

    There's a lot of ugly code here, relating to the extraction of different field types from
    the OGR feature. Don't worry too much about these details; the basic concept is that
    we extract the attribute's value, convert it to a string, and perform character encoding
    on the string if necessary.

    Finally, we'll have to add the following import statement to the top of the
    importer.py module::

        from geodjango.shapeEditor.models import AttributeValue

.. tab:: 英文

    Now that we've dealt with the feature's geometry, we can now look at importing the
    feature's attributes. The basic process involves iterating over the attributes, extracting
    the attribute value from the OGR feature, creating an AttributeValue object to store
    the value, and then saving it into the database::

        for attr in attributes:
            value = ...
            attr_value = AttributeValue(feature=feature,
                                        attribute=attr,
                                        value=value)
            attr_value.save()

    The challenge is to extract the attribute value from the feature. Because the OGR
    Feature object has different methods to extract different types of field values,
    we are going to have to check for the different field types, call the appropriate
    GetFieldAs() method, convert the resulting value to a string, and then store this
    string into the AttributeValue object. NULL values will also have to be handled
    appropriately. In addition, we have to deal with character encoding; any string
    values will have to be converted from the shapefile's character encoding into
    Unicode text so that they can be saved into the database. Because of this complexity,
    we'll define a new utils.py function to do the hard work, and simply call that
    function from import_data().

    Note that, because the user might have selected the wrong character encoding for
    the shapefile, the process of extracting the attribute value can actually fail. Because
    of this, we have to add error-handling to our code. To support error-handling, our
    utility function, get_ogr_feature_attribute(), will return a (success, result)
    tuple, where success will be true if and only if the attribute was successfully
    extracted, and result will either be the extracted attribute value (as a string),
    or an error message explaining why the operation failed.

    Let's add the necessary code to our import_data() function to store the
    attribute values into the database and gracefully handle any conversion
    errors that might occur::

        for attr in attributes:
            success,result = utils.getOGRFeatureAttribute(
                                                            attr, srcFeature,
                                                            character_encoding)
            if not success:
                os.remove(fname)
                shutil.rmtree(dst_dir)
                shapefile.delete()
                return result

            attr_value = AttributeValue(feature=feature,
                                        attribute=attr,
                                        value=result)
            attr_value.save()

    Note that we pass the Attribute object, the OGR feature, and the character encoding
    to the get_ogr_feature_attribute() function. If an error occurs, we clean up the
    temporary files, delete the shapefile we created earlier, and return the error message
    back to the caller. If the attribute was successfully extracted, we create a new
    AttributeValue object with the attribute's value, and save it into the database.

    .. note::

        Note that we use shapefile.delete() to remove the partially-imported shapefile from the database. By default, Django will also automatically delete any records that are related to the record being deleted through a ForeignKey field. This means that the Shapefile object will be deleted, along with all the related Attribute, Feature, and AttributeValue objects. With one line of code, we can completely remove all references to the shapefile's data.

    Now let's implement that get_ogr_feature_attribute() function. Add the following to utils.py::

        def getOGRFeatureAttribute(attr, feature, encoding):
            attr_name = str(attr.name)

            if not feature.IsFieldSet(attr_name):
                return (True, None)

            needs_encoding = False
            if attr.type == ogr.OFTInteger:
                value = str(feature.GetFieldAsInteger(attr_name))
            elif attr.type == ogr.OFTIntegerList:
                value = repr(feature.GetFieldAsIntegerList(attr_name))
            elif attr.type == ogr.OFTReal:
                value = feature.GetFieldAsDouble(attr_name)
                value = "%*.*f" % (attr.width, attr.precision, value)
            elif attr.type == ogr.OFTRealList:
                values = feature.GetFieldAsDoubleList(attr_name)
                str_values = []
                for value in values:
                    str_values.append("%*.*f" % (attr.width,
                                                attr.precision,
                                                value))
                value = repr(str_Values)
            elif attr.type == ogr.OFTString:
                value = feature.GetFieldAsString(attr_name)
                needs_encoding = True
            elif attr.type == ogr.OFTStringList:
                value = repr(feature.GetFieldAsStringList(attr_name))
                needs_encoding = True
            elif attr.type == ogr.OFTDate:
                parts = feature.GetFieldAsDateTime(attr_name)
                year,month,day,hour,minute,second,tzone = parts
                value = "%d,%d,%d,%d" % (year,month,day,tzone)
            elif attr.type == ogr.OFTTime:
                parts = feature.GetFieldAsDateTime(attr_name)
                year,month,day,hour,minute,second,tzone = parts
                value = "%d,%d,%d,%d" % (hour,minute,second,tzone)
            elif attr.type == ogr.OFTDateTime:
                parts = feature.GetFieldAsDateTime(attr_name)
                year,month,day,hour,minute,second,tzone = parts
                value = "%d,%d,%d,%d,%d,%d,%d,%d" % (year,month,day,
                                                     hour,minute,
                                                     second,tzone)
            else:
                return (False, "Unsupported attribute type: " +
                                str(attr.type))
            if needs_encoding:
                try:
                    value = value.decode(encoding)
                except UnicodeDecodeError:
                    return (False, "Unable to decode value in " +
                            repr(attr_name) + " attribute.&nbsp; " +
                            "Are you sure you're using the right " +
                            "character encoding?")
            return (True, value)

    There's a lot of ugly code here, relating to the extraction of different field types from
    the OGR feature. Don't worry too much about these details; the basic concept is that
    we extract the attribute's value, convert it to a string, and perform character encoding
    on the string if necessary.

    Finally, we'll have to add the following import statement to the top of the
    importer.py module::

        from geodjango.shapeEditor.models import AttributeValue


清理
-----------------------------------------
Cleaning up

.. tab:: 中文

    Now that we've imported the shapefile's data, all that's left is to clean up our
    temporary files and tell the caller that the import succeeded. To do this, simply
    add the following lines to the end of your import_data() function::

        os.remove(fname)
        shutil.rmtree(dst_dir)
        return None
    
    That's it!

    To test all this out, grab a copy of the TM_WORLD_BORDERS-0.3 shapefile in ZIP file
    format. You can either use the original ZIP archive that you downloaded from the
    World Borders Dataset website, or you can recompress the shapefile into a new ZIP
    archive. Then run the ShapeEditor, click on the **Import New Shapefile** button, click
    on **Browse...** and select the ZIP archive you want to import.

    Because the World Borders Dataset's features use the Latin1 character encoding, you
    need to make sure that this encoding is selected from the popup menu. Then click on
    **Submit**, and wait a few seconds for the shapefile to be imported. All going well, the
    world borders dataset will appear in the list of imported shapefiles:

    .. image:: ./img/430-0.png
       :align: center
    
    If a problem occurs, check the error message to see what might be wrong.
    Also, go back and make sure you have typed the code in exactly as described.
    If it works, congratulations! You have just implemented the most difficult part
    of the ShapeEditor. It gets easier from here.

.. tab:: 英文

    Now that we've imported the shapefile's data, all that's left is to clean up our
    temporary files and tell the caller that the import succeeded. To do this, simply
    add the following lines to the end of your import_data() function::

        os.remove(fname)
        shutil.rmtree(dst_dir)
        return None
    
    That's it!

    To test all this out, grab a copy of the TM_WORLD_BORDERS-0.3 shapefile in ZIP file
    format. You can either use the original ZIP archive that you downloaded from the
    World Borders Dataset website, or you can recompress the shapefile into a new ZIP
    archive. Then run the ShapeEditor, click on the **Import New Shapefile** button, click
    on **Browse...** and select the ZIP archive you want to import.

    Because the World Borders Dataset's features use the Latin1 character encoding, you
    need to make sure that this encoding is selected from the popup menu. Then click on
    **Submit**, and wait a few seconds for the shapefile to be imported. All going well, the
    world borders dataset will appear in the list of imported shapefiles:

    .. image:: ./img/430-0.png
       :align: center
    
    If a problem occurs, check the error message to see what might be wrong.
    Also, go back and make sure you have typed the code in exactly as described.
    If it works, congratulations! You have just implemented the most difficult part
    of the ShapeEditor. It gets easier from here.

