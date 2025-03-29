Mapnik 简介
============================================

Introducing Mapnik

.. tab:: 中文

    Mapnik is a powerful toolkit for using geospatial data to create maps. Mapnik can be downloaded from:
    
    http://mapnik.org
    
    Mapnik is a complex library with many different parts, and it is easy to get confused by the various names and concepts. Let's start our exploration of Mapnik by looking at a simple map:

    .. image:: ./img/303-0.png
       :scale: 80
       :class: with-border
       :align: center

    One thing that may not be immediately obvious is that the various elements within the map are layered, like this:

    .. image:: ./img/304-0.png
       :scale: 50
       :align: center

    To generate this map, you have to tell Mapnik to initially draw the background, then
    the polygons, and finally the labels. This ensures that the polygons sit on top of the
    background, and the labels appear in front of both the polygons and the background.
    
    .. note::

        Strictly speaking, the background isn't a layer. It's simply a color or image that Mapnik draws onto the map before it starts drawing the first layer.

    Mapnik allows you to control the order in which the map elements are drawn through
    the use of Layer objects. A simple map may consist of just one layer, but most maps
    have multiple layers. The layers are drawn in a strict back-to-front order, so the first
    layer you define will appear at the back. In the preceding example, the "Polygons"
    layer would be defined first, followed by the "Labels" layer, to ensure that the
    labels appear in front of the polygons. This layering approach is called the **painter's
    algorithm** because of its similarity to placing layers of paint onto an artist's canvas.

    Each Layer has its own data source, which tells Mapnik where to load the data from.
    A data source can refer to a shapefile, a spatial database, a raster image file, or any
    number of other geospatial data sources. In most cases, setting up a Layer's data
    source is very easy.

    Within each Layer, the visual display of the geospatial data is controlled through
    something called **Symbolizer**. While there are many different types of symbolizers
    available within Mapnik, three symbolizers are of interest to us here:

    - The PolygonSymbolizer is used to draw filled polygons:
    
    .. image:: ./img/305-0.png
       :class: with-border
       :align: center

    - The LineSymbolizer is used to draw the outline of polygons, as well as drawing LineStrings and other linear features, like this:
    
    .. image:: ./img/305-1.png
       :class: with-border
       :align: center
    
    - The TextSymbolizer is used to draw labels and other text onto the map:
    
    .. image:: ./img/305-2.png
       :class: with-border
       :align: center

    In many cases, these three symbolizers are enough to draw an entire map.
    Indeed, almost all of the preceding example maps was produced using just
    one PolygonSymbolizer, one LineSymbolizer, and one TextSymbolizer:
    
    .. image:: ./img/306-0.png
       :class: with-border
       :align: center

    Within each layer, the symbolizers are processed using the same "painter's algorithm"
    described earlier. In this case, the LineSymbolizer would be drawn on top of the
    PolygonSymbolizer.

    Note that the symbolizers aren't associated directly with a layer. Rather, there is an
    indirect association of symbolizers with a layer through the use of styles and rules.
    We'll look at styles in a minute, but for now let's take a closer look at the concept of
    a Mapnik rule.

    A **rule** allows a set of symbolizers to apply only when a given condition is met. For example, the map at the start of this chapter displayed Angola in a different color. This was done by defining two rules within the "Polygons" layer:
    
    .. image:: ./img/307-0.png
       :class: with-border
       :align: center

    The first rule has a **filter** that only applies to features that have a NAME attribute
    equal to the string Angola. For features that match this filter condition, the rule's
    *PolygonSymbolizer* will be used to draw the feature in dark red.

    The second rule has a similar filter, this time checking for features that don't have a
    NAME attribute equal to "Angola". These features are drawn using the second rule's
    *PolygonSymbolizer*, which draws the features in dark green.

    Obviously, rules can be very powerful in selectively changing the way features
    are displayed on a map. We'll be looking at rules in much more detail in the
    *Rules, filters and styles* section of this chapter.

    When you define your symbolizers, you place them into rules. The rules themselves
    are grouped into **styles**, which can be used to organize and keep track of your
    various rules. Each map layer itself has a list of the styles which apply to that
    particular layer.

    While this complex relationship between layers, styles, rules, filters, and symbolizers can be confusing, it also provides much of Mapnik's power and flexibility. It is important that you understand how these various classes work together:
    
    .. image:: ./img/308-0.png
       :class: with-border
       :align: center

    As you can see, you define the styles within the map itself, while the various layers
    refer to the styles that you have defined. This works in much the same way as a
    stylesheet in a word processing document, where you define styles and use them
    again and again. Note that the same style can be used in multiple layers.

    Finally, instead of using Python code to create the various Mapnik objects by hand,
    you can choose to use a **Map Definition File**. This is an XML-format file, which
    defines all the symbolizers, filters, rules, styles, and layers within a map. Your
    Python code then simply creates a new mapnik.Map object and tells Mapnik to
    load the map's contents from the XML definition file. This allows you to define the
    contents of your map separately from the Python code that does the map generation,
    in much the same way as an HTML templating engine separates form and content
    within a web application.

.. tab:: 英文

    Mapnik is a powerful toolkit for using geospatial data to create maps. Mapnik can be downloaded from:
    
    http://mapnik.org
    
    Mapnik is a complex library with many different parts, and it is easy to get confused by the various names and concepts. Let's start our exploration of Mapnik by looking at a simple map:

    .. image:: ./img/303-0.png
       :scale: 80
       :class: with-border
       :align: center

    One thing that may not be immediately obvious is that the various elements within the map are layered, like this:

    .. image:: ./img/304-0.png
       :scale: 50
       :align: center

    To generate this map, you have to tell Mapnik to initially draw the background, then
    the polygons, and finally the labels. This ensures that the polygons sit on top of the
    background, and the labels appear in front of both the polygons and the background.
    
    .. note::

        Strictly speaking, the background isn't a layer. It's simply a color or image that Mapnik draws onto the map before it starts drawing the first layer.

    Mapnik allows you to control the order in which the map elements are drawn through
    the use of Layer objects. A simple map may consist of just one layer, but most maps
    have multiple layers. The layers are drawn in a strict back-to-front order, so the first
    layer you define will appear at the back. In the preceding example, the "Polygons"
    layer would be defined first, followed by the "Labels" layer, to ensure that the
    labels appear in front of the polygons. This layering approach is called the **painter's
    algorithm** because of its similarity to placing layers of paint onto an artist's canvas.

    Each Layer has its own data source, which tells Mapnik where to load the data from.
    A data source can refer to a shapefile, a spatial database, a raster image file, or any
    number of other geospatial data sources. In most cases, setting up a Layer's data
    source is very easy.

    Within each Layer, the visual display of the geospatial data is controlled through
    something called **Symbolizer**. While there are many different types of symbolizers
    available within Mapnik, three symbolizers are of interest to us here:

    - The PolygonSymbolizer is used to draw filled polygons:
    
    .. image:: ./img/305-0.png
       :class: with-border
       :align: center

    - The LineSymbolizer is used to draw the outline of polygons, as well as drawing LineStrings and other linear features, like this:
    
    .. image:: ./img/305-1.png
       :class: with-border
       :align: center
    
    - The TextSymbolizer is used to draw labels and other text onto the map:
    
    .. image:: ./img/305-2.png
       :class: with-border
       :align: center

    In many cases, these three symbolizers are enough to draw an entire map.
    Indeed, almost all of the preceding example maps was produced using just
    one PolygonSymbolizer, one LineSymbolizer, and one TextSymbolizer:
    
    .. image:: ./img/306-0.png
       :class: with-border
       :align: center

    Within each layer, the symbolizers are processed using the same "painter's algorithm"
    described earlier. In this case, the LineSymbolizer would be drawn on top of the
    PolygonSymbolizer.

    Note that the symbolizers aren't associated directly with a layer. Rather, there is an
    indirect association of symbolizers with a layer through the use of styles and rules.
    We'll look at styles in a minute, but for now let's take a closer look at the concept of
    a Mapnik rule.

    A **rule** allows a set of symbolizers to apply only when a given condition is met. For example, the map at the start of this chapter displayed Angola in a different color. This was done by defining two rules within the "Polygons" layer:
    
    .. image:: ./img/307-0.png
       :class: with-border
       :align: center

    The first rule has a **filter** that only applies to features that have a NAME attribute
    equal to the string Angola. For features that match this filter condition, the rule's
    *PolygonSymbolizer* will be used to draw the feature in dark red.

    The second rule has a similar filter, this time checking for features that don't have a
    NAME attribute equal to "Angola". These features are drawn using the second rule's
    *PolygonSymbolizer*, which draws the features in dark green.

    Obviously, rules can be very powerful in selectively changing the way features
    are displayed on a map. We'll be looking at rules in much more detail in the
    *Rules, filters and styles* section of this chapter.

    When you define your symbolizers, you place them into rules. The rules themselves
    are grouped into **styles**, which can be used to organize and keep track of your
    various rules. Each map layer itself has a list of the styles which apply to that
    particular layer.

    While this complex relationship between layers, styles, rules, filters, and symbolizers can be confusing, it also provides much of Mapnik's power and flexibility. It is important that you understand how these various classes work together:
    
    .. image:: ./img/308-0.png
       :class: with-border
       :align: center

    As you can see, you define the styles within the map itself, while the various layers
    refer to the styles that you have defined. This works in much the same way as a
    stylesheet in a word processing document, where you define styles and use them
    again and again. Note that the same style can be used in multiple layers.

    Finally, instead of using Python code to create the various Mapnik objects by hand,
    you can choose to use a **Map Definition File**. This is an XML-format file, which
    defines all the symbolizers, filters, rules, styles, and layers within a map. Your
    Python code then simply creates a new mapnik.Map object and tells Mapnik to
    load the map's contents from the XML definition file. This allows you to define the
    contents of your map separately from the Python code that does the map generation,
    in much the same way as an HTML templating engine separates form and content
    within a web application.