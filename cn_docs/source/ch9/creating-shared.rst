创建共享应用程序
============================================

Creating the shared application

.. tab:: 中文

    `shapeEditor.shared` 应用程序将保存我们在整个系统中使用的核心数据库表和 Python 模块。现在让我们创建这个应用程序。进入 `shapeEditor` 项目目录并输入以下命令::

        python manage.py startapp shared

    这将在 `shapeEditor` 项目目录内创建一个名为 *shared* 的目录。该应用程序目录将包含 Django 运行所需的各类文件。

    请注意，默认情况下，新应用程序被放置在最顶层的 `shapeEditor` 目录中。这意味着您可以像这样将此应用程序导入到您的 Python 程序中::

        import shared

    Django 的惯例是，位于最顶层目录（或 Python 路径的其他位置）的应用程序是可重用的 —— 也就是说，您可以取出该应用程序并在不同的项目中使用。但是我们在这里定义的应用程序不是这样的；它们只能作为 `shapeEditor` 项目的一部分工作，因此我们需要将新创建的 `shared` 目录移动到 `shapeEditor` 项目的子目录内，如下所示：

    .. image:: ./img/393-0.png

    这意味着我们可以像这样导入我们的 `shared` 应用程序::

        import shapeEditor.shared

    或者::

        from shapeEditor import shared

    换句话说，`shared` 应用程序不是可重用的；它只在 `shapeEditor` 应用程序的上下文中有意义。

    .. note::

        不幸的是，Django 目前没有提供一种简便的方法来创建不可重用的应用程序。您必须先创建应用程序，然后将目录移动到项目目录中以使其不可重用。

    现在让我们看看 `shapeEditor.shared` 目录里面有什么：

    • ``__init__.py``
        这是另一个 Python 包初始化文件，告诉 Python 这个目录包含一个 Python 包。

    • ``models.py``
        这个 Python 模块将保存 `shared` 应用程序的数据模型。

    • ``tests.py``
        这个 Python 模块包含您应用程序的各种单元测试。我们不会用到这个模块，所以如果您愿意，可以删除此文件。

    • ``views.py``
        这个 Python 模块将保存 `shared` 应用程序的各种视图。同样地，我们不会用到这个模块，如果您想要，可以删除此文件。

    现在我们已经创建了应用程序本身，让我们将其添加到我们的项目中。再次编辑 `settings.py` 文件，并在 *INSTALLED_APPS* 列表中添加以下条目::

        'shapeEditor.shared'

    现在我们有了 `shared` 应用程序，让我们开始在其中放入一些有用的东西。

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
