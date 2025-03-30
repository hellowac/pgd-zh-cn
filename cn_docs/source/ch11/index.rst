11 ShapeEditor – 选择和编辑特征
============================================

11 ShapeEditor - Selecting and Editing Features

.. tab:: 中文

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
