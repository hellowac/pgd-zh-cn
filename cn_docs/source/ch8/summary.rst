小结
============================================

Summary

.. tab:: 中文

    在本章中，我们深入探索了 Mapnik 地图生成工具包。我们学到了以下内容：

    - Mapnik 是一个功能强大且灵活的工具包，用于生成各种类型的地图
    - Mapnik 使用画家算法以正确的顺序绘制地图的各个部分
    - 一张地图由多个图层组成
    - 地图渲染是通过样式来控制的
    - 样式在地图内定义，并通过图层进行引用，从而允许样式在多个图层之间共享
    - 每个样式由一个或多个规则组成
    - 每个规则有一个符号化器列表，告诉 Mapnik 如何将图层的要素绘制到地图上，并且可以有一个可选的过滤器，选择规则应用的要素
    - 你可以使用地图定义文件作为一种更简单的方式来创建地图，而不必在 Python 中定义所有的符号化器、过滤器、规则、样式和图层
    - 你可以将地图定义文件用作样式表，将构建地图的逻辑与其格式分开，这与 HTML 模板引擎在 Web 应用程序中将表单和内容分开的方式类似

    在下一章中，我们将开始构建一个完整的地图应用程序，使用 PostGIS、Mapnik 和 GeoDjango。

.. tab:: 英文

    In this chapter, we have explored the Mapnik map-generation toolkit in depth.
    We learned the following:

    - Mapnik is a powerful and flexible toolkit for generating a variety of maps
    - Mapnik uses the painter's algorithm to draw the various parts of a map in the correct order
    - A map is made up of multiple layers
    - Map rendering is controlled using styles
    - Styles are defined within the map and are referred to by the layers, allowing styles to be shared between map layers
    - Each style consists of one or more rules
    - Each rule has a list of symbolizers, telling Mapnik how to draw the layer's features onto the map, and an optional filter which selects the features the rule applies to
    - You can use a map definition file as a simpler way of creating maps without having to define all the symbolizers, filters, rules, styles, and layers in Python
    - You can use a map definition file as a stylesheet, separating the logic of building a map from the way it is formatted, in the same way that an HTML templating engine separates form and content in a web application

    In the next chapter, we will start to build a complete mapping application using PostGIS, Mapnik, and GeoDjango.
