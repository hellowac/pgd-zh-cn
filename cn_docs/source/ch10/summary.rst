小结
============================================

Summary

.. tab:: 中文

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
