设置 ShapeEditor 项目
============================================

Setting up the ShapeEditor project

.. tab:: 中文

    We now have to create the Django project which will hold the ShapeEditor system.
    To do this, cd into the directory where you want the project's directory to be placed,
    and type::

        % django-admin.py startproject shapeEditor

    When you installed Django, it should have placed the django-admin.py program
    onto your path, so you shouldn't need to tell the computer where this script resides.

    All going well, Django will create a directory named shapeEditor, which contains
    a python program named manage.py. You will use this program to start, stop, and
    configure your project, and another directory (also called shapeEditor) that holds
    the files that make up your project. Let's take a closer look at these files:
    
    - ``__init__.py``
        You should be familiar with this type of file; it simply tells Python that this directory holds a Python package.
    
    - ``settings.py``
        This Python module contains various settings for our shapeEditor project. These settings include options for turning debugging on or off, information about which database the Django project will use, where to find the project's URLConf module, and a list of the applications which should be included in the project.
    
    - ``urls.py``
        This is the URLConf module for the project. It maps incoming URLs to views within the project's applications.

    - ``wsgi.py``
        This module makes it possible to run your application using the **Web Server Gateway Interface (WSGI)** protocol. You'll use this when deploying your application to a production server.

    Now that the project has been created, we next need to configure it. To do this,
    edit the settings.py file. We want to make the following changes to this file:

    - Tell Django to use the PostGIS database we set up earlier for this project
    - Add the GeoDjango application to the project, to enable the GeoDjango functionality

    To tell Django to use PostGIS, edit the DATABASES variable to look like the following:

    .. code-block:: python

        DATABASES = {
            'default': {
                'ENGINE': 'django.contrib.gis.db.backends.postgis',
                'NAME':   'geodjango',
                'USER':   '...',
                'PASSWORD' : '...'
            }
        }

    Make sure you enter the username and password used to access your particular
    PostgreSQL database.

    To enable the GeoDjango functionality, add the following line to the INSTALLED_
    APPS variable at the bottom of the file::

        'django.contrib.gis'

    While we're editing the settings.py file, let's make one more change that will
    save us some trouble down the track. Go to the MIDDLEWARE_CLASSES setting, and
    comment out the *django.middleware.csrf.CsrfViewMiddleware* line. This entry
    causes the addition of extra error checking when processing forms to prevent cross-
    site request forgery. Implementing CSRF support requires adding extra code to our
    form templates, which we won't be doing here to keep things simple.

    .. note::

        If you deploy your own applications on the Internet, you should read the CSRF documentation on the Django website and enable CSRF support. Otherwise you may find your application subjected to cross-site request forgery attacks.
    
    This completes the configuration of our ShapeEditor project.

.. tab:: 英文

    We now have to create the Django project which will hold the ShapeEditor system.
    To do this, cd into the directory where you want the project's directory to be placed,
    and type::

        % django-admin.py startproject shapeEditor

    When you installed Django, it should have placed the django-admin.py program
    onto your path, so you shouldn't need to tell the computer where this script resides.

    All going well, Django will create a directory named shapeEditor, which contains
    a python program named manage.py. You will use this program to start, stop, and
    configure your project, and another directory (also called shapeEditor) that holds
    the files that make up your project. Let's take a closer look at these files:
    
    - ``__init__.py``
        You should be familiar with this type of file; it simply tells Python that this directory holds a Python package.
    
    - ``settings.py``
        This Python module contains various settings for our shapeEditor project. These settings include options for turning debugging on or off, information about which database the Django project will use, where to find the project's URLConf module, and a list of the applications which should be included in the project.
    
    - ``urls.py``
        This is the URLConf module for the project. It maps incoming URLs to views within the project's applications.

    - ``wsgi.py``
        This module makes it possible to run your application using the **Web Server Gateway Interface (WSGI)** protocol. You'll use this when deploying your application to a production server.

    Now that the project has been created, we next need to configure it. To do this,
    edit the settings.py file. We want to make the following changes to this file:

    - Tell Django to use the PostGIS database we set up earlier for this project
    - Add the GeoDjango application to the project, to enable the GeoDjango functionality

    To tell Django to use PostGIS, edit the DATABASES variable to look like the following:

    .. code-block:: python

        DATABASES = {
            'default': {
                'ENGINE': 'django.contrib.gis.db.backends.postgis',
                'NAME':   'geodjango',
                'USER':   '...',
                'PASSWORD' : '...'
            }
        }

    Make sure you enter the username and password used to access your particular
    PostgreSQL database.

    To enable the GeoDjango functionality, add the following line to the INSTALLED_
    APPS variable at the bottom of the file::

        'django.contrib.gis'

    While we're editing the settings.py file, let's make one more change that will
    save us some trouble down the track. Go to the MIDDLEWARE_CLASSES setting, and
    comment out the *django.middleware.csrf.CsrfViewMiddleware* line. This entry
    causes the addition of extra error checking when processing forms to prevent cross-
    site request forgery. Implementing CSRF support requires adding extra code to our
    form templates, which we won't be doing here to keep things simple.

    .. note::

        If you deploy your own applications on the Internet, you should read the CSRF documentation on the Django website and enable CSRF support. Otherwise you may find your application subjected to cross-site request forgery attacks.
    
    This completes the configuration of our ShapeEditor project.
