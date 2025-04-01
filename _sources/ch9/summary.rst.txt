小结
============================================

Summary

.. tab:: 中文

    您现在已经完成了 ShapeEditor 应用程序的第一部分的实现。即使在这个早期阶段，您已经取得了良好的进展，学习了 GeoDjango 的工作原理，设计了应用程序，并为接下来两章将要实现的功能奠定了基础。

    在本章中，您学习了以下内容：

    - GeoDjango 扩展 Django，可以用来构建复杂的地理空间 web 应用程序
    - 一个 Django 项目由单一的数据库和多个 Django 应用程序组成
    - 将一个复杂的项目拆分为多个较小的应用程序，并使它们协同工作
    - Django 使用对象表示数据库中的记录
    - Django 视图是一个 Python 函数，当调用特定 URL 时做出响应
    - 从 URL 到视图的映射由一个名为 `urls.py` 的 URLConf 模块控制，该模块在项目级别定义
    - Django 使用强大的模板系统来简化复杂 HTML 页面创建
    - Django 允许您定义表单来处理数据输入
    - Django 表单字段使得接受和验证各种不同类型的数据变得简单
    - GeoDjango 提供了一组用于编辑地理空间数据的表单字段
    - 应用程序的数据对象在一个名为 `models.py` 的文件中定义
    - GeoDjango 内置的“admin”系统允许您使用交互式地图查看和编辑地理空间数据

    在*第 10 章，ShapeEditor – 实现列表视图、导入和导出*中，我们将实现一个视图来显示可用的 Shapefile，并编写相当复杂的代码来导入和导出 Shapefile。

.. tab:: 英文

    You have now finished implementing the first part of the ShapeEditor application.
    Even at this early stage, you have made good progress, learning how GeoDjango
    works, designing the application, and laying the foundations for the functionality
    you will implement in the next two chapters.

    In this chapter, you have learned the following:
    
    - The GeoDjango extension to Django can be used to build sophisticated geospatial web applications
    - A Django project consists of a single database and multiple Django applications
    - Breaking a complex project into a number of smaller applications and making them all work together
    - Django uses objects to represent records in the database
    - A Django view is a Python function, which responds when a given URL is called
    - The mapping from URLs to views is controlled by a URLConf module named urls.py defined at the project level
    - Django uses a powerful templating system to simplify the creation of complex HTML pages
    - Django allows you to define forms for handling the input of data
    - Django form fields make it easy to accept and validate a variety of different types of data
    - GeoDjango provides its own set of form fields for editing geospatial data
    - An application's data objects are defined in a file called models.py
    - GeoDjango's built-in "admin" system allows you to view and edit geospatial data using slippy maps

    In *Chapter 10, ShapeEditor – Implementing List View, Import, and Export*, we will
    implement a view to show the available shapefiles, as well as write rather complex
    code for importing and exporting shapefiles.
