关于 ShapeEditor
============================================

About ShapeEditor

.. tab:: 中文

    As we have seen, shapefiles are commonly used to store, make available, and
    transfer geospatial data. We have worked with shapefiles extensively in this book,
    obtaining freely-available geospatial data in shapefile format, writing programs to
    load data from a shapefile, and creating shapefiles programmatically.

    While it is easy enough to edit the attributes associated with a shapefile's features,
    editing the features themselves is a lot more complicated. One approach would be
    to install a GIS system and use it to import the data, make changes, and then export
    the data into another shapefile. While this works, it is hardly convenient if all you
    want to do is make a few changes to a shapefile's features. It would be much easier
    if we had a web application specifically designed for editing shapefiles.

    This is precisely what we are going to implement: a web-based shapefile editor.
    Rather unimaginatively, we'll call this program ShapeEditor.

    The following flowchart depicts the ShapeEditor's basic workflow:

    .. image:: ./img/371-0.png
       :align: center

    The user starts by importing a shapefile using the ShapeEditor's web interface:

    .. image:: ./img/372-0.png
       :align: center
    
    .. note::

        Our ShapeEditor implementation wasn't chosen for its good
        looks; instead, it concentrates on getting the features working.
        It would be easy to add stylesheets and edit the HTML
        templates to improve the appearance of the application, but
        doing so would make the code harder to understand. This
        is why we've taken such a minimalist approach to the user
        interface. Making it pretty is an exercise left to the reader.

    Once the shapefile has been imported, the user can view the shapefile's features on
    a map, and can select a feature by clicking on it. In this case, we have imported the
    World Borders Dataset used several times throughout this book:

    .. image:: ./img/372-1.png
       :align: center

    The user can then edit the selected feature's geometry, as well as see a list of the attributes associated with that feature:

    .. image:: ./img/373-0.png
       :align: center

    Once the user has finished making changes to the shapefile, he or she can export the shapefile again by clicking on the **Export** hyperlink on the main page:

    .. image:: ./img/374-0.png
       :align: center

    That pretty much covers the ShapeEditor's functionality. It's a comparatively simple
    system, but it can be very useful if you need to work with geospatial data in shapefile
    format. And, of course, through the process of implementing the ShapeEditor you will
    learn how to go about implementing your own complex geospatial web applications
    using GeoDjango.
    
.. tab:: 英文

    As we have seen, shapefiles are commonly used to store, make available, and
    transfer geospatial data. We have worked with shapefiles extensively in this book,
    obtaining freely-available geospatial data in shapefile format, writing programs to
    load data from a shapefile, and creating shapefiles programmatically.

    While it is easy enough to edit the attributes associated with a shapefile's features,
    editing the features themselves is a lot more complicated. One approach would be
    to install a GIS system and use it to import the data, make changes, and then export
    the data into another shapefile. While this works, it is hardly convenient if all you
    want to do is make a few changes to a shapefile's features. It would be much easier
    if we had a web application specifically designed for editing shapefiles.

    This is precisely what we are going to implement: a web-based shapefile editor.
    Rather unimaginatively, we'll call this program ShapeEditor.

    The following flowchart depicts the ShapeEditor's basic workflow:

    .. image:: ./img/371-0.png
       :align: center

    The user starts by importing a shapefile using the ShapeEditor's web interface:

    .. image:: ./img/372-0.png
       :align: center
    
    .. note::

        Our ShapeEditor implementation wasn't chosen for its good
        looks; instead, it concentrates on getting the features working.
        It would be easy to add stylesheets and edit the HTML
        templates to improve the appearance of the application, but
        doing so would make the code harder to understand. This
        is why we've taken such a minimalist approach to the user
        interface. Making it pretty is an exercise left to the reader.

    Once the shapefile has been imported, the user can view the shapefile's features on
    a map, and can select a feature by clicking on it. In this case, we have imported the
    World Borders Dataset used several times throughout this book:

    .. image:: ./img/372-1.png
       :align: center

    The user can then edit the selected feature's geometry, as well as see a list of the attributes associated with that feature:

    .. image:: ./img/373-0.png
       :align: center

    Once the user has finished making changes to the shapefile, he or she can export the shapefile again by clicking on the **Export** hyperlink on the main page:

    .. image:: ./img/374-0.png
       :align: center

    That pretty much covers the ShapeEditor's functionality. It's a comparatively simple
    system, but it can be very useful if you need to work with geospatial data in shapefile
    format. And, of course, through the process of implementing the ShapeEditor you will
    learn how to go about implementing your own complex geospatial web applications
    using GeoDjango.
