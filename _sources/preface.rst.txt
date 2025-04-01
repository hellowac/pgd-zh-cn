前言
===========

Preface

.. tab:: 中文

    随着基于地图的网站以及空间感知设备和应用程序的激增，地理空间开发变得越来越重要。地理空间市场正在迅速增长，作为一名 Python 开发人员，您不能落后。在当今的位置感知世界中，所有商业 Python 开发人员都可以从对地理空间概念和开发技术的理解中受益。

    处理地理空间数据可能会变得复杂，因为您要处理的是地球表面的数学模型。由于 Python 是一种功能强大的编程语言，具有高级工具包，因此它非常适合地理空间开发。本书将让您熟悉地理空间开发所需的 Python 工具。

    它介绍了基本的地理空间概念，并清晰详细地介绍了位置、距离、单位、投影、基准和地理空间数据格式等关键概念。然后，我们研究了许多 Python 库，并将它们与免费提供的地理空间数据一起使用来完成各种任务。本书深入介绍了将空间数据存储在数据库中的概念，以及如何使用空间数据库作为解决各种地理空间问题的工具。它详细介绍了使用 Mapnik 地图渲染工具包生成地图的方法，并帮助您使用 GeoDjango、Mapnik 和 PostGIS 构建复杂的基于 Web 的地理空间地图编辑应用程序。在本书的最后，您将能够将空间功能集成到您的应用程序中，并从头开始构建完整的地图应用程序。

    本书是一本实践教程。它教您如何使用一系列 Python 工具高效地访问、操作和显示地理空间数据以进行 GIS 开发。

.. tab:: 英文

    With the explosion of map-based websites and spatially-aware devices and applications, geospatial development is becoming increasingly important. The geospatial market is growing rapidly, and as a Python developer you can't afford to be left behind. In today's location-aware world, all commercial Python developers can benefit from an understanding of geospatial concepts and development techniques.

    Working with geospatial data can get complicated because you are dealing with mathematical models of the Earth's surface. Since Python is a powerful programming language with high-level toolkits, it is well-suited to geospatial development. This book will familiarize you with the Python tools required for geospatial development.

    It introduces basic geospatial concepts with a clear, detailed walkthrough of the key concepts such as location, distance, units, projections, datums, and geospatial data formats. We then examine a number of Python libraries and use these with freely- available geospatial data to accomplish a variety of tasks. The book provides an in- depth look at the concept of storing spatial data in a database and how you can use spatial databases as tools to solve a variety of geospatial problems. It goes into the details of generating maps using the Mapnik map-rendering toolkit, and helps you to build a sophisticated web-based geospatial map editing application using GeoDjango, Mapnik, and PostGIS. By the end of the book, you will be able to integrate spatial features into your applications and build a complete mapping application from scratch.

    This book is a hands-on tutorial. It teaches you how to access, manipulate, and display geospatial data efficiently using a range of Python tools for GIS development.

本书涵盖的内容
---------------------------

What this book covers

.. tab:: 中文

    *第 1 章，使用 Python 进行地理空间开发*，概述了 Python 编程语言和地理空间开发背后的概念。还介绍了地理空间开发的主要用例以及该领域近期和未来的发展。

    *第 2 章，GIS*，介绍了位置、距离、单位、投影、形状、基准和地理空间数据格式的核心概念，然后讨论了手动处理地理空间数据的过程。

    *第 3 章，用于地理空间开发的 Python 库*，探讨了可用于地理空间开发的主要 Python 库，包括可用功能、库的组织方式以及如何安装和使用它。

    *第 4 章，地理空间数据来源*，研究了可免费获取的地理空间数据的主要来源、可用的信息、使用的数据格式以及如何导入下载的数据。

    *第 5 章，使用 Python 处理地理空间数据*，使用前面介绍的库来执行各种使用地理空间数据的任务，包括更改投影、导入和导出数据、转换和标准化几何和距离单位以及执行地理空间计算。

    *第 6 章，数据库中的 GIS*，介绍 PostGIS、MySQL 和 SQLite 的空间功能。它讨论了存储不同类型空间数据的最佳实践，并研究了如何从 Python 访问这些数据库。

    *第 7 章，使用空间数据*，使用存储在空间数据库中的免费地理空间数据，设计和实现了一个完整的地理空间应用程序 DISTAL。它研究了此应用程序的性能，然后使用最佳实践技术对其进行了优化。

    *第 8 章，使用 Python 和 Mapnik 制作地图*，深入介绍了 Mapnik 地图生成工具包，以及如何使用它来制作各种地图。

    *第 9 章，将所有内容整合在一起：一个完整​​的地图应用程序*，介绍了“ShapeEditor”，这是一个使用 PostGIS、Mapnik 和 GeoDjango 构建的完整而复杂的 Web 应用程序。我们首先设计整个应用程序，然后构建 ShapeEditor 的数据库模型。

    *第 10 章，ShapeEditor：实现列表视图、导入和导出*，继续实现 ShapeEditor 系统，重点介绍显示导入的 Shapefile 列表，以及通过 Web 浏览器导入和导出 Shapefile 的逻辑。

    *第 11 章，ShapeEditor：选择和编辑特征*，总结了 ShapeEditor 的实现，添加了逻辑以让用户在导入的 Shapefile 中选择和编辑特征。这涉及创建自定义 Tile 地图服务器，以及使用 OpenLayers JavaScript 库显示和与地理空间数据交互。

    *附加章节“地理空间开发的 Web 框架”* 探讨了 Web 应用程序框架、Web 服务、JavaScript UI 库和滑动地图的概念。它介绍了地理空间应用程序使用的许多标准 Web 协议，最后概述了可用于构建通过 Web 界面运行的地理空间应用程序的工具和框架。

    您可以从以下位置下载本章：http://www.packtpub.com/sites/default/files/downloads/1523OS_Bonuschapter.pdf

.. tab:: 英文


    *Chapter 1, Geospatial Development Using Python*, gives an overview of the Python programming language and the concepts behind geospatial development. Major use-cases of geospatial development and recent and upcoming developments in the field are also covered.

    *Chapter 2, GIS*, introduces the core concepts of location, distance, units, projections, shapes, datums, and geospatial data formats, before discussing the process of working with geospatial data manually.

    *Chapter 3, Python Libraries for Geospatial Development*, explores the major Python libraries available for geospatial development, including the available features, how the library is organized, and how to install and use it.

    *Chapter 4, Sources of Geospatial Data*, investigates the major sources of freely-available geospatial data, what information is available, the data format used, and how to import the downloaded data.

    *Chapter 5, Working with Geospatial Data in Python*, uses the libraries introduced earlier to perform various tasks using geospatial data, including changing projections, importing and exporting data, converting and standardizing units of geometry and distance, and performing geospatial calculations.

    *Chapter 6, GIS in the Database*, introduces the spatial capabilities of PostGIS, MySQL, and SQLite. It discusses best practices for storing different types of spatial data, and looks at how to access these databases from Python.

    *Chapter 7, Working with Spatial Data*, works through the design and implementation of a complete geospatial application called DISTAL, using freely-available geospatial data stored in a spatial database. It investigates the performance of this application and then works to optimize it using best-practice techniques.

    *Chapter 8, Using Python and Mapnik to Produce Maps*, gives an in-depth look at the
    Mapnik map-generation toolkit, and how to use it to produce a variety of maps.

    *Chapter 9, Putting it all Together: a Complete Mapping Application*, introduces the "ShapeEditor", a complete and sophisticated web application built using PostGIS, Mapnik and GeoDjango. We start by designing the overall application, and then build the ShapeEditor's database models.

    *Chapter 10, ShapeEditor: Implementing List View, Import, and Export*, continues the implementation of the ShapeEditor system, concentrating on displaying a list of imported shapefiles, along with the logic for importing and exporting shapefiles via a web browser.

    *Chapter 11, ShapeEditor: Selecting and Editing Features*, concludes the implementation of the ShapeEditor, adding logic to let the user select and edit features within an imported shapefile. This involves the creation of a custom Tile Map Server, and the use of the OpenLayers JavaScript library to display and interact with geospatial data.

    *Bonus chapter, Web Frameworks for Geospatial Development*, examines the concepts of web application frameworks, web services, JavaScript UI libraries, and slippy maps. It introduces a number of standard web protocols used by geospatial applications, and finishes with a survey of the tools and frameworks available for building geospatial applications that run via a web interface.

    You can download this chapter from: http://www.packtpub.com/sites/default/files/downloads/1523OS_Bonuschapter.pdf

本书的阅读需求
----------------------------------------------

What you need for this book

.. tab:: 中文

    要阅读本书，您需要拥有 Python 2.5 到 2.7 版本。您还需要下载并安装以下工具和库；完整说明请参阅本书的相关章节：

    * GDAL/OGR
    * GEOS
    * Shapely
    * Proj
    * pyproj
    * MySQL
    * MySQLdb
    * SpatiaLite
    * pysqlite
    * PostgreSQL
    * PostGIS
    * pyscopg2
    * Mapnik* Django

.. tab:: 英文

    To follow through this book, you will need to have Python Version 2.5 to 2.7. You will also need to download and install the following tools and libraries; full instructions are given in the relevant sections of this book:

    * GDAL/OGR
    * GEOS
    * Shapely
    * Proj
    * pyproj
    * MySQL
    * MySQLdb
    * SpatiaLite
    * pysqlite
    * PostgreSQL
    * PostGIS
    * pyscopg2
    * Mapnik* Django

本书的读者对象
----------------------------------------------

Who this book is for

.. tab:: 中文

    本书面向经验丰富的 Python 开发人员，他们希望快速掌握开源地理空间工具和技术，以便构建自己的地理空间应用程序，或将地理空间技术集成到现有的 Python 程序中。

.. tab:: 英文

    This book is aimed at experienced Python developers who want to get up to speed with open source geospatial tools and techniques in order to build their own geospatial applications, or to integrate geospatial technology into their existing Python programs.

惯例
----------------------------------------------

Conventions

.. tab:: 中文

    在本书中，您将看到多种文本样式，用于区分不同类型的信息。以下是一些这些样式的示例，并附有它们的含义解释。

    **文本中的代码词** 以如下方式显示：  
    “`The pyproj Geod class allows you to perform various geodetic calculations based on points on the Earth's surface.`”

    **代码块** 以如下方式呈现::

        import mapnik

        symbolizer = mapnik.PolygonSymbolizer(mapnik.Color("darkgreen"))

        rule = mapnik.Rule()
        rule.symbols.append(symbolizer)

    当我们希望引起您对代码块中特定部分的注意时，相关的行或项会以 **粗体** 显示：

        import mapnik

        symbolizer = mapnik.PolygonSymbolizer(mapnik.Color("darkgreen"))

        rule = mapnik.Rule()  # 粗体代码
        rule.symbols.append(symbolizer)

    任何 **命令行输入或输出** 以如下方式书写：

    .. code-block:: shell

        python setup.py build
        sudo python.setup.py install

    **新术语** 和 **重要词汇** 以 **粗体** 显示。您在屏幕、菜单或对话框中看到的词语，举例来说，像这样显示：“点击 **Next** 按钮将您带到下一个屏幕”。

.. tab:: 英文

    In this book, you will find a number of styles of text that distinguish between different kinds of information. Here are some examples of these styles, and an explanation of their meaning.

    Code words in text are shown as follows: " The pyproj Geod class allows you to perform various geodetic calculations based on points on the Earth's surface."

    A block of code is set as follows::

        import mapnik

        symbolizer = mapnik.PolygonSymbolizer(mapnik.Color("darkgreen"))

        rule = mapnik.Rule()
        rule.symbols.append(symbolizer)

    When we wish to draw your attention to a particular part of a code block, the relevant lines or items are set in bold::

        import mapnik
        symbolizer = mapnik.PolygonSymbolizer(mapnik.Color("darkgreen"))
        rule = mapnik.Rule()  # blod code
        rule.symbols.append(symbolizer)

    Any command-line input or output is written as follows:

    .. code-block:: shell

        python setup.py build
        sudo python.setup.py install

    **New terms** and **important words** are shown in bold. Words that you see on the screen, in menus or dialog boxes for example, appear in the text like this: "clicking the **Next** button moves you to the next screen".

读者反馈
----------------------------------------------

Reader feedback

.. tab:: 中文

    我们随时欢迎读者的反馈。让我们知道您对这本书的看法——您喜欢或不喜欢什么。读者反馈对我们开发真正让您受益匪浅的图书非常重要。

    要向我们发送一般反馈，只需发送电子邮件至 *feedback@packtpub.com*，并在邮件主题中提及书名。

    如果您对某个主题很了解，并且有兴趣撰写或参与撰写书籍，请参阅我们的作者指南 www.packtpub.com/authors。

.. tab:: 英文

    Feedback from our readers is always welcome. Let us know what you think about this book—what you liked or may have disliked. Reader feedback is important for us to develop titles that you really get the most out of.

    To send us general feedback, simply send an e-mail to *feedback@packtpub.com*, and mention the book title via the subject of your message.

    If there is a topic that you have expertise in and you are interested in either writing or contributing to a book, see our author guide on www.packtpub.com/authors.

客户支持
----------------------------------------------

Customer support

.. tab:: 中文

    现在您已是 Packt 书籍的骄傲拥有者，我们会采取多种措施来帮助您从购买的书籍中获得最大收益。

.. tab:: 英文


    Now that you are the proud owner of a Packt book, we have a number of things to help you to get the most from your purchase.

下载示例代码
----------------------------------------------

Downloading the example code

.. tab:: 中文

    您可以从 http://www.packtpub.com 上的帐户下载您购买的所有 Packt 书籍的示例代码文件。如果您在其他地方购买了本书，您可以访问 http://www.packtpub.com/support 并注册，以便直接通过电子邮件将文件发送给您。

.. tab:: 英文


    You can download the example code files for all Packt books you have purchased from your account at http://www.packtpub.com. If you purchased this book elsewhere, you can visit http://www.packtpub.com/support and register to have the files e-mailed directly to you.

勘误表
----------------------------------------------

Errata

.. tab:: 中文

    尽管我们已尽一切努力确保内容的准确性，但错误还是难免。如果您在我们的书中发现错误（可能是文本或代码中的错误），我们将非常感激您向我们报告此错误。这样做可以避免其他读者感到沮丧，并帮助我们改进本书的后续版本。如果您发现任何勘误，请访问 http://www.packtpub.com/submit-errata ，选择您的书，单击 **勘误表提交表单链接** ，然后输入您的勘误表详细信息，以进行报告。一旦您的勘误表得到验证，您的提交将被接受，勘误表将上传到我们的网站，或添加到该书名的勘误表部分下的任何现有勘误表列表中。从 http://www.packtpub.com/support 选择您的书名即可查看任何现有勘误表。

.. tab:: 英文

    Although we have taken every care to ensure the accuracy of our content, mistakes do happen. If you find a mistake in one of our books—maybe a mistake in the text or the code—we would be grateful if you would report this to us. By doing so, you can save other readers from frustration and help us improve subsequent versions of this book. If you find any errata, please report them by visiting http://www.packtpub. com/submit-errata, selecting your book, clicking on the **errata submission form link**, and entering the details of your errata. Once your errata are verified, your submission will be accepted and the errata will be uploaded on our website, or added to any list of existing errata, under the Errata section of that title. Any existing errata can be viewed by selecting your title from http://www.packtpub.com/support.

盗版
----------------------------------------------

Piracy

.. tab:: 中文

    互联网上版权材料的盗版是所有媒体中持续存在的问题。在 Packt，我们非常重视对版权和许可的保护。如果您在互联网上发现任何形式的我们作品的非法副本，请立即向我们提供位置地址或网站名称，以便我们采取补救措施。

    请通过 copyright@packtpub.com 与我们联系，并提供疑似盗版材料的链接。

    我们感谢您帮助保护我们的作者，并感谢您让我们能够为您带来有价值的内容。

.. tab:: 英文


    Piracy of copyright material on the Internet is an ongoing problem across all media. At Packt, we take the protection of our copyright and licenses very seriously. If you come across any illegal copies of our works, in any form, on the Internet, please provide us with the location address or website name immediately so that we can pursue a remedy.

    Please contact us at copyright@packtpub.com with a link to the suspected pirated material.

    We appreciate your help in protecting our authors, and our ability to bring you valuable content.

问题
----------------------------------------------

Questions

.. tab:: 中文

    如果您对本书的任何方面有疑问，可以通过 questions@packtpub.com 联系我们，我们将尽力解决。

.. tab:: 英文

    You can contact us at questions@packtpub.com if you are having a problem with any aspect of the book, and we will do our best to address it.