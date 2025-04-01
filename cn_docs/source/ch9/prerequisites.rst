先决条件
============================================

Prerequisites

.. tab:: 中文

    在构建 ShapeEditor 应用程序之前，请确保您已经安装了在《第3章，地理空间开发的 Python 库》和《第6章，数据库中的 GIS》中介绍的以下库和工具：

    - OGR
    - Mapnik
    - PROJ.4
    - pyproj
    - PostgreSQL
    - PostGIS
    - psycopg2

    您还需要下载并安装 Django。Django（https://djangoproject.com）自带了 GeoDjango，因此一旦安装了 Django，您就可以开始使用了。请点击 Django 网站上的 **Download** 链接，下载最新的官方版本 Django 软件。我们将在 ShapeEditor 中使用 Django 1.4。

    如果您的计算机运行的是 Microsoft Windows，您可能需要下载一个工具来解压 *.tar.gz* 文件，然后才能使用它。

    下载完成后，您可以按照 Django 安装指南中的说明进行安装。指南可以在以下链接找到：

    https://docs.djangoproject.com/en/dev/topics/install

    安装完成后，您可能希望先浏览 GeoDjango 教程（可以在 https://docs.djangoproject.com/en/dev/ref/contrib/gis 中找到），尽管这对于构建 ShapeEditor 应用程序并不是必需的。

.. tab:: 英文

    Before you can build the ShapeEditor application, make sure that you have installed
    the following libraries and tools introduced in *Chapter 3, Python Libraries for Geospatial
    Development and Chapter 6, GIS in the Database*:

    - OGR
    - Mapnik
    - PROJ.4
    - pyproj
    - PostgreSQL
    - PostGIS
    - psycopg2

    You will also need to download and install Django. Django (https://djangoproject.
    com) comes with GeoDjango built-in, so once you've installed Django itself you're all
    set to go. Click on the **Download** link on the Django website and download the latest
    official version of the Django software. We'll be using Django 1.4 for the ShapeEditor.

    If your computer runs Microsoft Windows, you may need to download a utility to
    decompress the *.tar.gz* file before you can use it.
    
    Once you have downloaded it, you can install Django by following the instructions
    in the Django Installation Guide. This can be found at:
    
    https://docs.djangoproject.com/en/dev/topics/install
    
    Once you have installed it, you may want to run through the GeoDjango tutorial
    (available at https://docs.djangoproject.com/en/dev/ref/contrib/gis),
    though this isn't required to build the ShapeEditor application.
