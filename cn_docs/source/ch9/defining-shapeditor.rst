定义 ShapeEditor 的应用程序
============================================

Defining the ShapeEditor's applications

.. tab:: 中文

    We now have a Django project for our overall ShapeEditor system. We next need to
    break down our project into several related applications, following Django's design
    philosophy of having applications be small and relatively self-contained. Looking
    back at our design for the overall project, we can see several possible candidates for
    breaking the functionality into separate applications:

    - Importing and exporting shapefiles
    - Selecting features
    - Editing features
    - The Tile Map Server
    
    Let's choose some names for our applications, keeping them short and to the point:
    
    - importer
    - exporter
    - selector
    - editor
    - tms
    
    We will define one more application, which we'll called shared, to hold the
    database models and Python modules that are shared across these applications.
    For example, we might have a module named attributeReader.py that is needed
    by the importer and editor applications. We'll place this into the shared application
    to make it clear that this code is designed to be used elsewhere in the system.

.. tab:: 英文

    We now have a Django project for our overall ShapeEditor system. We next need to
    break down our project into several related applications, following Django's design
    philosophy of having applications be small and relatively self-contained. Looking
    back at our design for the overall project, we can see several possible candidates for
    breaking the functionality into separate applications:

    - Importing and exporting shapefiles
    - Selecting features
    - Editing features
    - The Tile Map Server
    
    Let's choose some names for our applications, keeping them short and to the point:
    
    - importer
    - exporter
    - selector
    - editor
    - tms
    
    We will define one more application, which we'll called shared, to hold the
    database models and Python modules that are shared across these applications.
    For example, we might have a module named attributeReader.py that is needed
    by the importer and editor applications. We'll place this into the shared application
    to make it clear that this code is designed to be used elsewhere in the system.
