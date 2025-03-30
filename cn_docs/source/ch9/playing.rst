玩转管理系统
============================================

Playing with the admin system

.. tab:: 中文

    Before we can use the built-in admin application, we will need to enable it.
    This involves adding the admin application to the project, syncing the database,
    telling the admin application about our database objects, and adding the admin
    URLs to our urls.py file. Let's work through each of these in turn:
    
    - Adding the admin application to the project:
      Edit your settings.py file and uncomment the 'django.contrib.admin' line within the INSTALLED_APPS list::

        INSTALLED_APPS = (
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.sites',
            'django.contrib.messages',
            # Uncomment the next line to enable the admin:
            'django.contrib.admin',
            'django.contrib.gis',
            'shapeEditor'
        )
    
    - Resynchronizing the database:
      From the command line, cd into your GeoDjango project directory and type the following::
    
        python manage.py syncdb
    
      This will add the admin application's tables to your database.
    
    - Adding our database objects to the admin interface:
      Next, we need to tell the Admin interface about the various database objects we want to work with. To do this, create a new file in the shapeEditor. shared directory named admin.py, and enter the following into this file::

        from django.contrib.gis import admin
        from models import Shapefile, Feature, \
             Attribute, AttributeValue

        admin.site.register(Shapefile, admin.ModelAdmin)
        admin.site.register(Feature, admin.GeoModelAdmin)
        admin.site.register(Attribute, admin.ModelAdmin)
        admin.site.register(AttributeValue, admin.ModelAdmin)
      
      This tells Django how to display the various objects in the admin interface. If you want, you can subclass admin.ModelAdmin (or admin.GeoModelAdmin) and customize how it works. For now, we'll just accept the defaults.

      .. note::

         Note that we use an admin.GeoModelAdmin object for the Feature class. This is because the Feature objects include geometries that we want to edit using a slippy map. We'll see how this works shortly.

    - Adding the admin URLs to the project:
      Edit the urls.py file (in the shapeEditor project directory) and uncomment the lines that refer to the admin application. Then change the from django. contrib import admin line to read::

        from django.contrib.gis import admin

      The following listing shows how this file should end up, with the three lines you need to change highlighted::

        from django.conf.urls.defaults import *

        # Uncomment the next two lines to enable the admin:
        from django.contrib.gis import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            # Example:
            # (r'^geodjango/', include('geodjango.foo.urls')),
            # Uncomment the admin/doc line below and add 'django.contrib.
        admindocs'
            # to INSTALLED_APPS to enable admin documentation:
            # (r'^admin/doc/', include('django.contrib.admindocs.urls')),
            # Uncomment the next line to enable the admin:
            (r'^admin/', include(admin.site.urls)),
        )

    When this is done, it is time to run the application. cd into the main GeoDjango project directory and type::

        python manage.py runserver

    This will start up the Django server for your project. Open a web browser and navigate to the following URL:

    http://127.0.0.1:8000/admin/shared

    You should see the Django administration Log in page as shown in the following screenshot:

    .. image:: ./img/403-0.png
       :align: center

    Enter the username and password for the superuser you created earlier, and you will see the main admin interface for the ShapeEditor.shared application as shown in the following screenshot:

    .. image:: ./img/404-0.png
       :align: center

    Let's use this admin interface to create a dummy shapefile. Click on the **Add** link on the **Shapefiles** row, and you will be presented with a basic input screen for entering a new shapefile:

    .. image:: ./img/404-1.png
       :align: center

    Enter some dummy values into the various fields (it doesn't matter what you enter), and click on the **Save** button to save the new Shapefile object into the database. A list of the shapefiles that are present in the database will be shown. At the moment, there is only the shapefile you just created:

    .. image:: ./img/405-0.png
       :align: center

    As you can see, the new shapefile object has been given a rather unhelpful label:
    Shapefile object. This is because we haven't yet told Django what textual label
    to use for a shapefile (or any of our other database objects). To fix this, edit the
    shared.models file and add the following method to the end of the Shapefile
    class definition::

        def __unicode__(self):
            return self.filename

    The __unicode__ method returns a human-readable summary of the Shapefile
    object's contents. In this case, we are showing the filename associated with the
    shapefile. If you then reload the web page, you can see that the shapefile now
    has a useful label:

    .. image:: ./img/406-0.png
       :align: center

    Go ahead and add the __unicode__ methods to the other model objects as well::

        class Attribute(models.Model):
            ...
            def __unicode__(self):
                return self.name

        class Feature(models.Model):
            ...
            def __unicode__(self):
                return str(self.id)

        class AttributeValue(models.Model):
            ...
            def __unicode__(self):
                return self.value

    While this may seem like busywork, it's actually quite useful to have your database
    objects able to describe themselves. If you wanted to, you could further customize
    the admin interface, for example by showing the attributes and features associated
    with the selected shapefile. For now, though, let's take a look at GeoDjango's built-in
    geometry editors.

    Go back to the shared application's administration page (by clicking on the **Shared**
    hyperlink near the top of the window), and click on the **Add** button in the **Features**
    row. As with the shapefile, you will be asked to enter the details for a new feature.
    This time, however, the admin interface will use a slippy map to enter each of the
    different geometry types supported by the Feature object:

    .. image:: ./img/407-0.png
       :align: center

    Obviously, having multiple slippy maps like this isn't quite what we want, and if we
    wanted we could set up a custom GeoModelAdmin subclass to avoid this, but that's
    not important right now. Instead, try selecting the shapefile with which you want to
    associate this feature by choosing your shapefile from the pop-up menu, and then
    scroll down to the Geom Multipolygon field and try adding a couple of polygons to
    the map.

    To do this, click on the map to add points to the current polygon, or hold down the
    Shift key and click to finish the current polygon. The interface can be a bit confusing
    at first, but it's certainly usable. We'll look at the various options for editing polygons
    later. For now, just click on **Save** to save your new feature. If you edit it again, you'll
    see your saved geometry (or geometries) once again on the slippy maps.

    Make sure you add at least two polygons. The built-in admin view will show an
    error if you try to save a single polygon into a MultiPolygon field. Note that this is
    only a problem with the built-in admin view; when we write the editing code for
    the ShapeEditor, this limitation won't apply.

    That completes our tour of the admin interface. We won't be using this for end users,
    as we don't want to require users to log in before making changes to the shapefile
    data. We will, however, be borrowing some code from the admin application so that
    end users can edit their shapefile features using a slippy map.

.. tab:: 英文

    Before we can use the built-in admin application, we will need to enable it.
    This involves adding the admin application to the project, syncing the database,
    telling the admin application about our database objects, and adding the admin
    URLs to our urls.py file. Let's work through each of these in turn:
    
    - Adding the admin application to the project:
      Edit your settings.py file and uncomment the 'django.contrib.admin' line within the INSTALLED_APPS list::

        INSTALLED_APPS = (
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.sites',
            'django.contrib.messages',
            # Uncomment the next line to enable the admin:
            'django.contrib.admin',
            'django.contrib.gis',
            'shapeEditor'
        )
    
    - Resynchronizing the database:
      From the command line, cd into your GeoDjango project directory and type the following::
    
        python manage.py syncdb
    
      This will add the admin application's tables to your database.
    
    - Adding our database objects to the admin interface:
      Next, we need to tell the Admin interface about the various database objects we want to work with. To do this, create a new file in the shapeEditor. shared directory named admin.py, and enter the following into this file::

        from django.contrib.gis import admin
        from models import Shapefile, Feature, \
             Attribute, AttributeValue

        admin.site.register(Shapefile, admin.ModelAdmin)
        admin.site.register(Feature, admin.GeoModelAdmin)
        admin.site.register(Attribute, admin.ModelAdmin)
        admin.site.register(AttributeValue, admin.ModelAdmin)
      
      This tells Django how to display the various objects in the admin interface. If you want, you can subclass admin.ModelAdmin (or admin.GeoModelAdmin) and customize how it works. For now, we'll just accept the defaults.

      .. note::

         Note that we use an admin.GeoModelAdmin object for the Feature class. This is because the Feature objects include geometries that we want to edit using a slippy map. We'll see how this works shortly.

    - Adding the admin URLs to the project:
      Edit the urls.py file (in the shapeEditor project directory) and uncomment the lines that refer to the admin application. Then change the from django. contrib import admin line to read::

        from django.contrib.gis import admin

      The following listing shows how this file should end up, with the three lines you need to change highlighted::

        from django.conf.urls.defaults import *

        # Uncomment the next two lines to enable the admin:
        from django.contrib.gis import admin
        admin.autodiscover()

        urlpatterns = patterns('',
            # Example:
            # (r'^geodjango/', include('geodjango.foo.urls')),
            # Uncomment the admin/doc line below and add 'django.contrib.
        admindocs'
            # to INSTALLED_APPS to enable admin documentation:
            # (r'^admin/doc/', include('django.contrib.admindocs.urls')),
            # Uncomment the next line to enable the admin:
            (r'^admin/', include(admin.site.urls)),
        )

    When this is done, it is time to run the application. cd into the main GeoDjango project directory and type::

        python manage.py runserver

    This will start up the Django server for your project. Open a web browser and navigate to the following URL:

    http://127.0.0.1:8000/admin/shared

    You should see the Django administration Log in page as shown in the following screenshot:

    .. image:: ./img/403-0.png
       :align: center

    Enter the username and password for the superuser you created earlier, and you will see the main admin interface for the ShapeEditor.shared application as shown in the following screenshot:

    .. image:: ./img/404-0.png
       :align: center

    Let's use this admin interface to create a dummy shapefile. Click on the **Add** link on the **Shapefiles** row, and you will be presented with a basic input screen for entering a new shapefile:

    .. image:: ./img/404-1.png
       :align: center

    Enter some dummy values into the various fields (it doesn't matter what you enter), and click on the **Save** button to save the new Shapefile object into the database. A list of the shapefiles that are present in the database will be shown. At the moment, there is only the shapefile you just created:

    .. image:: ./img/405-0.png
       :align: center

    As you can see, the new shapefile object has been given a rather unhelpful label:
    Shapefile object. This is because we haven't yet told Django what textual label
    to use for a shapefile (or any of our other database objects). To fix this, edit the
    shared.models file and add the following method to the end of the Shapefile
    class definition::

        def __unicode__(self):
            return self.filename

    The __unicode__ method returns a human-readable summary of the Shapefile
    object's contents. In this case, we are showing the filename associated with the
    shapefile. If you then reload the web page, you can see that the shapefile now
    has a useful label:

    .. image:: ./img/406-0.png
       :align: center

    Go ahead and add the __unicode__ methods to the other model objects as well::

        class Attribute(models.Model):
            ...
            def __unicode__(self):
                return self.name

        class Feature(models.Model):
            ...
            def __unicode__(self):
                return str(self.id)

        class AttributeValue(models.Model):
            ...
            def __unicode__(self):
                return self.value

    While this may seem like busywork, it's actually quite useful to have your database
    objects able to describe themselves. If you wanted to, you could further customize
    the admin interface, for example by showing the attributes and features associated
    with the selected shapefile. For now, though, let's take a look at GeoDjango's built-in
    geometry editors.

    Go back to the shared application's administration page (by clicking on the **Shared**
    hyperlink near the top of the window), and click on the **Add** button in the **Features**
    row. As with the shapefile, you will be asked to enter the details for a new feature.
    This time, however, the admin interface will use a slippy map to enter each of the
    different geometry types supported by the Feature object:

    .. image:: ./img/407-0.png
       :align: center

    Obviously, having multiple slippy maps like this isn't quite what we want, and if we
    wanted we could set up a custom GeoModelAdmin subclass to avoid this, but that's
    not important right now. Instead, try selecting the shapefile with which you want to
    associate this feature by choosing your shapefile from the pop-up menu, and then
    scroll down to the Geom Multipolygon field and try adding a couple of polygons to
    the map.

    To do this, click on the map to add points to the current polygon, or hold down the
    Shift key and click to finish the current polygon. The interface can be a bit confusing
    at first, but it's certainly usable. We'll look at the various options for editing polygons
    later. For now, just click on **Save** to save your new feature. If you edit it again, you'll
    see your saved geometry (or geometries) once again on the slippy maps.

    Make sure you add at least two polygons. The built-in admin view will show an
    error if you try to save a single polygon into a MultiPolygon field. Note that this is
    only a problem with the built-in admin view; when we write the editing code for
    the ShapeEditor, this limitation won't apply.
    
    That completes our tour of the admin interface. We won't be using this for end users,
    as we don't want to require users to log in before making changes to the shapefile
    data. We will, however, be borrowing some code from the admin application so that
    end users can edit their shapefile features using a slippy map.
