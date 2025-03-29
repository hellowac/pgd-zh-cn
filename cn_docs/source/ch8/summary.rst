小结
============================================

Summary

.. tab:: 中文

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
