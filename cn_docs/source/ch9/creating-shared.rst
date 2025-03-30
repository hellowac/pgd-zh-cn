创建共享应用程序
============================================

Creating the shared application

.. tab:: 中文

    The shapeEditor.shared application will hold the core database tables and python
    modules we use throughout the system. Let's go ahead and create this application
    now. cd into the shapeEditor project directory and type the following::

        python manage.py startapp shapeEditor
    
    This will create a directory within the shapeEditor project directory named *shared*.
    This application directory will contain various files Django needs in order to run.

    Note that, by default, a new application is placed in the topmost shapeEditor
    directory. This means you can import this application into your Python program
    like this::

        import shared
    
    Django's conventions say that applications in the topmost directory (or somewhere
    else on your Python path) are intended to be reusable—that is, you can take that
    application and use it in a different project. The applications we're defining here
    aren't like that; they can only work as part of the shapeEditor project, and so we
    need to move the newly-created shared directory inside the shapeEditor project's
    sub-directory, like this:

    .. image:: ./img/393-0.png

    This means we can import our shared application like this::

        import shapeEditor.shared
    
    or::

        from shapeEditor import shared

    In other words, the shared application isn't reusable; it only makes sense within the
    context of the shapeEditor application.

    .. note::

        Unfortunately, Django doesn't currently make it easy for you to create non-reusable applications. You have to create the application first, and then move the directory into the project directory to make it non-reusable.

    Let's now take a look at what is inside our shapeEditor.shared directory:
    
    - ``__init__.py``
        This is another Python package initialization file, telling Python that this directory holds a Python package.

    - ``models.py``
        This Python module will hold the shared application's data models.
    
    - ``tests.py``
        This Python module holds various unit tests for your application. We won't be using this, so you can delete this file if you wish.
    
    - ``views.py``
        This Python module will hold various views for the shared application. Once again, we won't be using this, and you can delete this file if you want.
    
    Now that we have created the application itself, let's add it to our project. Edit the
    settings.py file again, and add the following entry to the *INSTALLED_APPS* list:
    
        'shapeEditor.shared'
    
    Now that we have our shared application, let's start to put some useful things into it.

.. tab:: 英文

    The shapeEditor.shared application will hold the core database tables and python
    modules we use throughout the system. Let's go ahead and create this application
    now. cd into the shapeEditor project directory and type the following::

        python manage.py startapp shapeEditor
    
    This will create a directory within the shapeEditor project directory named *shared*.
    This application directory will contain various files Django needs in order to run.

    Note that, by default, a new application is placed in the topmost shapeEditor
    directory. This means you can import this application into your Python program
    like this::

        import shared
    
    Django's conventions say that applications in the topmost directory (or somewhere
    else on your Python path) are intended to be reusable—that is, you can take that
    application and use it in a different project. The applications we're defining here
    aren't like that; they can only work as part of the shapeEditor project, and so we
    need to move the newly-created shared directory inside the shapeEditor project's
    sub-directory, like this:

    .. image:: ./img/393-0.png

    This means we can import our shared application like this::

        import shapeEditor.shared
    
    or::

        from shapeEditor import shared

    In other words, the shared application isn't reusable; it only makes sense within the
    context of the shapeEditor application.

    .. note::

        Unfortunately, Django doesn't currently make it easy for you to create non-reusable applications. You have to create the application first, and then move the directory into the project directory to make it non-reusable.

    Let's now take a look at what is inside our shapeEditor.shared directory:
    
    - ``__init__.py``
        This is another Python package initialization file, telling Python that this directory holds a Python package.

    - ``models.py``
        This Python module will hold the shared application's data models.
    
    - ``tests.py``
        This Python module holds various unit tests for your application. We won't be using this, so you can delete this file if you wish.
    
    - ``views.py``
        This Python module will hold various views for the shared application. Once again, we won't be using this, and you can delete this file if you want.
    
    Now that we have created the application itself, let's add it to our project. Edit the
    settings.py file again, and add the following entry to the *INSTALLED_APPS* list:
    
        'shapeEditor.shared'
    
    Now that we have our shared application, let's start to put some useful things into it.
