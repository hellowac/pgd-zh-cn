11 ShapeEditor – 选择和编辑特征
============================================

11 ShapeEditor - Selecting and Editing Features

.. tab:: 中文

   在本章中，我们将实现 ShapeEditor 应用程序的剩余功能。本章的很大一部分将涉及使用 OpenLayers 和创建一个瓦片地图服务器 (Tile Map Server)，以便我们可以在地图上显示 shapefile 的所有特征，并允许用户点击某个特征进行选择。我们还将实现添加、编辑和删除特征的功能，并最终探讨 ShapeEditor 如何用于处理地理空间数据，以及它如何成为你自己地理空间开发工作的跳板。

   在本章中，我们将学习：

   - 如何使用 Mapnik 和 GeoDjango 实现瓦片地图服务器 (Tile Map Server)
   - 如何使用 OpenLayers 在网页上显示平滑地图 (slippy map)
   - 如何为 OpenLayers 编写自定义点击事件处理程序 (click-handler)
   - 如何在 OpenLayers 中使用 AJAX 请求
   - 如何使用 GeoDjango 执行空间查询
   - 如何在自己的应用程序中使用 GeoDjango 内置的编辑小部件
   - 如何使用 GeoDjango 内置的编辑小部件编辑地理空间数据
   - 如何自定义 GeoDjango 编辑小部件的界面
   - 如何在 Django Web 应用程序中添加和删除记录

   让我们从实现让用户选择要编辑的特征的代码开始吧。

.. tab:: 英文

   In this final chapter, we will implement the remaining features of the ShapeEditor
   application. A large part of this chapter will involve the use of OpenLayers and the
   creation of a Tile Map Server so that we can display a map with all the shapefile's
   features on it, and allow the user to click on a feature to select it. We'll also implement
   the ability to add, edit, and delete features, and conclude with a exploration of how
   the ShapeEditor can be used to work with geospatial data, and how it can serve as
   the springboard for your own geospatial development efforts.

   In this chapter, we will learn:
   
   - How to implement a Tile Map Server using Mapnik and GeoDjango
   - How to use OpenLayers to display a slippy map on a web page
   - How to write a custom click-handler for OpenLayers
   - How to use AJAX requests within OpenLayers
   - How to perform spatial queries using GeoDjango
   - How to use GeoDjango's built-in editing widgets in your own application
   - How to edit geospatial data using GeoDjango's built-in editing widgets
   - How to customize the interface for GeoDjango's editing widgets
   - How to add and delete records in a Django web application

   Let's get started with the code that lets the user select the feature to be edited.

.. toctree::
   :maxdepth: 2
   :caption: 内容

   ./selecting.rst
   ./editring.rst
   ./adding.rst
   ./deleting.rst
   ./deleting-shapefiles.rst
   ./using.rst
   ./further.rst
   ./summary.rst
