设置 ShapeEditor 项目
============================================

Setting up the ShapeEditor project

.. tab:: 中文

    现在我们必须创建一个 Django 项目来承载 ShapeEditor 系统。
    为此，进入您希望放置项目目录的目录，然后输入::

        % django-admin.py startproject shapeEditor

    安装 Django 时，它应该已经将 `django-admin.py` 程序添加到了您的系统路径中，因此您无需告诉计算机此脚本的位置。

    如果一切顺利，Django 将创建一个名为 `shapeEditor` 的目录，其中包含一个名为 `manage.py` 的 Python 程序。您将使用此程序来启动、停止和配置您的项目，以及另一个同名（也称为 `shapeEditor`）的目录，该目录包含构成您项目的文件。让我们更详细地查看这些文件：

    • ``__init__.py``
        您应该熟悉这种类型的文件；它只是告诉 Python 这个目录包含一个 Python 包。

    • ``settings.py``
        这个 Python 模块包含我们 `shapeEditor` 项目的各种设置。这些设置包括开启或关闭调试的选项、关于 Django 项目将使用哪个数据库的信息、在哪里找到项目的 URLConf 模块，以及应包含在项目中的应用程序列表。

    • ``urls.py``
        这是项目的 URLConf 模块。它将传入的 URL 映射到项目应用程序中的视图。

    • ``wsgi.py``
        该模块使您能够使用 **Web 服务器网关接口 (WSGI)** 协议运行您的应用程序。在将应用程序部署到生产服务器时将使用此模块。

    现在项目已经创建，接下来我们需要对其进行配置。为此，请编辑 `settings.py` 文件。我们希望对文件进行以下更改：

    • 告诉 Django 使用我们之前设置的 PostGIS 数据库
    • 将 GeoDjango 应用程序添加到项目中，以启用 GeoDjango 功能

    要告诉 Django 使用 PostGIS，请将 `DATABASES` 变量编辑成如下所示：

    .. code-block:: python

        DATABASES = {
            'default': {
                'ENGINE': 'django.contrib.gis.db.backends.postgis',
                'NAME':   'geodjango',
                'USER':   '...',
                'PASSWORD' : '...'
            }
        }

    确保输入用于访问您的特定 PostgreSQL 数据库的用户名和密码。

    要启用 GeoDjango 功能，请在文件末尾的 `INSTALLED_APPS` 变量中添加以下行::

        'django.contrib.gis'

    在编辑 `settings.py` 文件时，我们再做一个更改，这将在以后为我们省去一些麻烦。转到 `MIDDLEWARE_CLASSES` 设置，并注释掉 *django.middleware.csrf.CsrfViewMiddleware* 这一行。此条目会在处理表单时添加额外的错误检查以防止跨站请求伪造。实现 CSRF 支持需要在我们的表单模板中添加额外的代码，为了简单起见，我们在这里不这样做。

    .. note::

        如果您在互联网上部署自己的应用程序，应该阅读 Django 网站上的 CSRF 文档并启用 CSRF 支持。否则，您的应用程序可能会遭受跨站请求伪造攻击。

    至此，我们完成了 ShapeEditor 项目的配置。

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
