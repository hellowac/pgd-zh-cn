小结
============================================

Summary

.. tab:: 中文

    在本章中，我们使用 GeoDjango、Mapnik、PostGIS、OGR 和 pyproj 完成了一个复杂的地理空间 Web 应用程序的实现。这个应用程序本身就很有用，同时也是开发你自己地理空间 Web 应用程序的一个起点。

    我们学到了：

    - 我们可以使用 Mapnik 和 GeoDjango 轻松创建自己的瓦片地图服务器。
    - 我们可以在自己的网页上独立于 GeoDjango 包含 OpenLayers，并显示来自我们瓦片地图服务器的地图数据。
    - 如何创建一个自定义的“点击处理器”来响应 OpenLayers 地图中的鼠标点击。
    - 我们可以使用 AJAX 调用让服务器响应 Web 浏览器中的事件。
    - GeoDjango 提供了一种强大的查询语言，可以在不编写一行 SQL 的情况下执行空间查询。
    - 如何“借用” GeoDjango 的几何编辑小部件并在你自己的 Web 应用程序中使用它们。
    - 你可以创建自己的 GeoModelAdmin 子类来改变 GeoDjango 几何编辑小部件的外观和功能。
    - 你可以使用一个简单的 HTML 表单来确认记录的删除。

    这就完成了我们对 GeoDjango 的探索，也完成了本书。希望你已经学到了很多关于地理空间开发的知识，以及如何使用 Python 创建地理空间应用程序。有了这些工具，你现在可以开始开发自己复杂的地理空间系统了。玩得开心！

.. tab:: 英文

    In this chapter, we finished implementing a sophisticated geospatial web application
    using GeoDjango, Mapnik, PostGIS, OGR, and pyproj. This application is useful
    in its own right, as well as being a springboard to developing your own geospatial
    web applications.

    We have learned:

    - That we can easily create our own Tile Map Server using Mapnik and GeoDjango.
    - That we can include OpenLayers on our own web pages, independent of GeoDjango, and display map data from our Tile Map Server.
    - How to create a custom "click handler" to respond to mouse-clicks within an OpenLayers map.
    - That we can use AJAX calls to have the server respond to events within the web browser.
    - That GeoDjango provides a powerful query language for performing spatial queries without writing a single line of SQL.
    - How to "borrow" geometry editing widgets from GeoDjango and use them within your own web application.
    - That you can create your own GeoModelAdmin subclass to change the appearance and functionality of GeoDjango's geometry editing widgets.
    - That you can use a simple HTML form to confirm the deletion of a record.

    This completes our exploration of GeoDjango, and also completes this book. Hopefully you have learned a lot about geospatial development, and how to create geospatial applications using Python. With these tools at your disposal, you are now ready to start developing your own complex geospatial systems. Have fun!
