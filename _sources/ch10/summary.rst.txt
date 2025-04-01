小结
============================================

Summary

.. tab:: 中文

    在本章中，我们继续实现了 ShapeEditor，通过添加三个重要功能：**列表视图**，以及导入和导出 shapefile 的功能。虽然这些功能看起来不是很激动人心，但它们是 ShapeEditor 的核心组成部分。

    在实现这些功能的过程中，我们学到了以下几点：

    - 使用 Django 的模板语言在网页中显示记录列表。
    - 使用 Python 的 `zipfile` 标准库模块解压上传的 shapefile 的内容，然后使用 OGR 打开该 shapefile。
    - 在将 shapefile 数据导入到 PostGIS 数据库时，您需要“包装”多边形 (Polygon) 和线条 (LineString) 几何图形，以避免 shapefile 无法区分多边形和多重多边形 (MultiPolygon)，以及线条和多重线条 (MultiLineString) 的问题。
    - 当调用对象的 `delete()` 方法时，Django 会自动删除所有相关的记录，这简化了删除记录及其所有关联数据的过程。
    - 您可以使用 OGR 创建新的 shapefile，并使用 `zipfile` 库模块将其压缩，以便通过 Web 界面导出地理空间数据。

    随着这些功能的实现完成，我们现在可以将注意力转向 ShapeEditor 最有趣的部分：显示并让用户使用平滑地图接口编辑几何图形的代码。这将是本书最后一章的主要内容。

.. tab:: 英文

    In this chapter, we continued our implementation of the ShapeEditor by adding three
    important functions: the "list" view, and the ability to import and export shapefiles.
    While these aren't very exciting features, they are a crucial part of the ShapeEditor.

    In the process of implementing these features, we have learned the following:

    - Using Django's templating language to display a list of records within a web page.
    - Using the zipfile standard library module to extract the contents of an uploaded shapefile before opening that shapefile using OGR.
    - You will need to "wrap" Polygon and LineString geometries when importing data from a shapefile into a PostGIS database, to avoid problems caused by a shapefile's inability to distinguish between Polygons and MultiPolygons, and between LineStrings and MultiLineStrings.
    - When you call the object.delete() method, Django automatically deletes all the linked records for you, simplifying the process of removing a record and all its associated data.
    - You can use OGR to create a new shapefile, and the zipfile library module to compress it, so that you can export geospatial data using a web interface.

    With this functionality out of the way, we can now turn our attention to the most
    interesting parts of the ShapeEditor: the code that displays and lets the user edit
    geometries using a slippy map interface. This will be the main focus for the final
    chapter of this book.
