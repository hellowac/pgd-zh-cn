10 ShapeEditor – 实现列表视图、导入和导出
============================================

10 ShapeEditor – Implementing List View, Import, and Export

.. tab:: 中文

   在本章中，我们将继续实现 ShapeEditor 应用程序。我们将从实现一个“列表”视图来显示可用的 shapefile 开始，然后通过网络界面处理 shapefile 的导入和导出细节。

   在本章中，我们将学习以下内容：

   • 使用 Django 模板显示记录列表
   • 处理 shapefile 数据的复杂性，包括几何体和属性数据类型的问题
   • 使用网络界面导入 shapefile 的数据
   • 使用网络界面导出 shapefile

   让我们首先实现用户在开始运行 ShapeEditor 时将看到的视图。

.. tab:: 英文

   In this chapter we continue our implementation of the ShapeEditor application.
   We will start by implementing a "list" view to show the available shapefiles, and then
   work through the details of importing and exporting shapefiles via a web interface.

   In this chapter, we will learn the following:

   - Displaying a list of records using a Django template
   - Dealing with the complexities of shapefile data, including issues with geometries and attribute data types
   - Importing a shapefile's data using a web interface
   - Exporting a shapefile using a web interface

   Let's start by implementing the view the user will see when they start running
   the ShapeEditor.

.. toctree::
   :maxdepth: 2
   :caption: 内容

   ./implementing.rst
   ./importing.rst
   ./exporting.rst
   ./summary.rst
