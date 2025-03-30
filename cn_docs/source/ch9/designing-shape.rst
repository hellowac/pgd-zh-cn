设计 ShapeEditor
============================================

Designing ShapeEditor

.. tab:: 中文

    Let's take a closer look at the various parts of the ShapeEditor, to see what's involved
    in implementing it. The ShapeEditor is going to support the following activities:
    
    - Importing the geometrical features and attributes from a shapefile
    - Allowing the user to select a feature to be edited
    - Displaying the appropriate type of editor to allow the user to edit the feature's geometry
    - Exporting the geometrical features and attributes back into a shapefile

    Let's take a closer look at each of these user activities, to see how they will be
    implemented within the ShapeEditor system.

.. tab:: 英文

    Let's take a closer look at the various parts of the ShapeEditor, to see what's involved
    in implementing it. The ShapeEditor is going to support the following activities:
    
    - Importing the geometrical features and attributes from a shapefile
    - Allowing the user to select a feature to be edited
    - Displaying the appropriate type of editor to allow the user to edit the feature's geometry
    - Exporting the geometrical features and attributes back into a shapefile

    Let's take a closer look at each of these user activities, to see how they will be
    implemented within the ShapeEditor system.

导入 Shapefile
-------------------------
Importing a shapefile

.. tab:: 中文

    When the user imports a shapefile, we have to store the contents of that shapefile
    in the database so that GeoDjango can work with it. Because we don't know in
    advance what types of geometries the shapefile will contain, or what attributes
    might be associated with each feature, we need to have a generic representation
    of a shapefile's contents in the database rather than defining separate fields in
    the database for each of the shapefile's attributes.To support this, we'll use the
    following collection of database objects:
  
    .. image:: ./img/375-0.png
       :align: center

    Each imported shapefile will be represented by a single Shapefile object in the
    database. Each Shapefile object will have a set of Attribute objects, which define
    the name and data type for each attribute within the shapefile. The Shapefile object
    will also have a set of Feature objects, one for each imported feature. The Feature
    object will hold the feature's geometry, and will in turn have a set of AttributeValue
    objects, holding the value of each attribute for that feature.

    To see how this works, let's imagine that we import the World Borders Dataset into
    the ShapeEditor. The contents of this shapefile would be stored in the database in
    the following way:
  
    .. image:: ./img/376-0.png
       :align: center

    We will use a *Shapefile* object to represent the uploaded shapefile. This object
    will have a number of *Attribute* objects associated with it, one for each of the
    shapefile's attributes. There are also a number of *Feature* objects associated with
    the shapefile; the MultiPolygon geometry for each feature will be stored in the
    *Feature* object itself, while the attributes for each feature will be stored in a
    series of *AttributeValue* objects.

    While this is a somewhat roundabout way of storing shapefile data in a database
    (it would be more common to use the *ogrinspect* management command to create
    a static GeoDjango model out of the shapefile's features and attributes), we have to
    do it this way because we don't know the shapefile's structure ahead of time, and
    don't want to have to define a new database table whenever a shapefile is imported.
    
    With this basic model in place to represent a shapefile's data in the database, we can
    continue designing the rest of the "Import Shapefile" logic.

    Because shapefiles are represented on disk by a number of separate files, we will
    expect the user to create a ZIP archive out of the shapefile and upload the zipped
    shapefile. This saves us having to handle multiple file uploads for a single shapefile,
    and makes things more convenient for the user as shapefiles often come in ZIP
    format already.

    Once the ZIP archive has been uploaded, our code will need to decompress the
    archive and extract the individual files that make up the shapefile. We'll then
    have to read through the shapefile to find its attributes, create the appropriate
    *Attribute* objects, and then process the shapefile's features one at a time,
    creating *Feature* and *AttributeValue* objects as we go. All of this will be
    quite straightforward to implement.

.. tab:: 英文

    When the user imports a shapefile, we have to store the contents of that shapefile
    in the database so that GeoDjango can work with it. Because we don't know in
    advance what types of geometries the shapefile will contain, or what attributes
    might be associated with each feature, we need to have a generic representation
    of a shapefile's contents in the database rather than defining separate fields in
    the database for each of the shapefile's attributes.To support this, we'll use the
    following collection of database objects:
  
    .. image:: ./img/375-0.png
       :align: center

    Each imported shapefile will be represented by a single Shapefile object in the
    database. Each Shapefile object will have a set of Attribute objects, which define
    the name and data type for each attribute within the shapefile. The Shapefile object
    will also have a set of Feature objects, one for each imported feature. The Feature
    object will hold the feature's geometry, and will in turn have a set of AttributeValue
    objects, holding the value of each attribute for that feature.

    To see how this works, let's imagine that we import the World Borders Dataset into
    the ShapeEditor. The contents of this shapefile would be stored in the database in
    the following way:
  
    .. image:: ./img/376-0.png
       :align: center

    We will use a *Shapefile* object to represent the uploaded shapefile. This object
    will have a number of *Attribute* objects associated with it, one for each of the
    shapefile's attributes. There are also a number of *Feature* objects associated with
    the shapefile; the MultiPolygon geometry for each feature will be stored in the
    *Feature* object itself, while the attributes for each feature will be stored in a
    series of *AttributeValue* objects.

    While this is a somewhat roundabout way of storing shapefile data in a database
    (it would be more common to use the *ogrinspect* management command to create
    a static GeoDjango model out of the shapefile's features and attributes), we have to
    do it this way because we don't know the shapefile's structure ahead of time, and
    don't want to have to define a new database table whenever a shapefile is imported.
    
    With this basic model in place to represent a shapefile's data in the database, we can
    continue designing the rest of the "Import Shapefile" logic.

    Because shapefiles are represented on disk by a number of separate files, we will
    expect the user to create a ZIP archive out of the shapefile and upload the zipped
    shapefile. This saves us having to handle multiple file uploads for a single shapefile,
    and makes things more convenient for the user as shapefiles often come in ZIP
    format already.

    Once the ZIP archive has been uploaded, our code will need to decompress the
    archive and extract the individual files that make up the shapefile. We'll then
    have to read through the shapefile to find its attributes, create the appropriate
    *Attribute* objects, and then process the shapefile's features one at a time,
    creating *Feature* and *AttributeValue* objects as we go. All of this will be
    quite straightforward to implement.


选择要素
-------------------------
Selecting a feature

.. tab:: 中文

    Before the user can edit a feature, we have to let the user select that feature.
    Unfortunately, GeoDjango's build-in slippy map interface won't allow us to select a
    feature by clicking on it. This is because GeoDjango can only display a single feature
    on a map at once, thanks to the way GeoDjango's geometry fields are implemented.

    The usual way a GeoDjango application allows you to select a feature is by
    displaying a list of attributes (for example, city names) and then allowing the user to
    choose a feature from that list. Unfortunately, that won't work for us either. Because
    the ShapeEditor allows the user to import any shapefile, there's no guarantee that the
    shapefile's attribute values can be used to select a feature. It may be that a shapefile
    has no attributes at all, or has attributes that mean nothing to the end user—or,
    conversely has dozens of attributes. There is no way of knowing which attribute to
    display, or even if there is a suitable attribute that can be used to select a feature.
    Because of this, we really can't use attributes when selecting the feature to edit.
    
    We're going to take a completely different approach. We will bypass GeoDjango's
    built-in editor and instead use OpenLayers directly to display a map showing all
    the features in the imported shapefile. We'll then let the user click on a feature
    within the map to select it for editing.

    Here is how we'll implement this particular feature:

    .. image:: ./img/378-0.png
       :align: center

    OpenLayers needs to have a source of map tiles to display, so we'll create our own
    simple **Tile Map Server (TMS)** built on top of a Mapnik-based map renderer to
    display the shapefile's features stored in the database. We'll also write a simple "click
    handler" in JavaScript that intercepts clicks on the map and sends off an AJAX request
    to the server to see which feature the user clicked on. If the user does click on a feature
    (rather than just clicking on the map's background), the user's web browser will be
    redirected to the "Edit Feature" page so that the user can edit the clicked-on feature.

    There's a lot here, requiring a fair amount of custom coding, but the end result is
    a friendly interface to the ShapeEditor, allowing the user to simply point-and-click
    at a desired feature to edit it. In the process of building all this, we'll also learn how
    to use OpenLayers directly within a GeoDjango application, and how to implement
    our own Tile Map Server built on top of Mapnik.

.. tab:: 英文

    Before the user can edit a feature, we have to let the user select that feature.
    Unfortunately, GeoDjango's build-in slippy map interface won't allow us to select a
    feature by clicking on it. This is because GeoDjango can only display a single feature
    on a map at once, thanks to the way GeoDjango's geometry fields are implemented.

    The usual way a GeoDjango application allows you to select a feature is by
    displaying a list of attributes (for example, city names) and then allowing the user to
    choose a feature from that list. Unfortunately, that won't work for us either. Because
    the ShapeEditor allows the user to import any shapefile, there's no guarantee that the
    shapefile's attribute values can be used to select a feature. It may be that a shapefile
    has no attributes at all, or has attributes that mean nothing to the end user—or,
    conversely has dozens of attributes. There is no way of knowing which attribute to
    display, or even if there is a suitable attribute that can be used to select a feature.
    Because of this, we really can't use attributes when selecting the feature to edit.
    
    We're going to take a completely different approach. We will bypass GeoDjango's
    built-in editor and instead use OpenLayers directly to display a map showing all
    the features in the imported shapefile. We'll then let the user click on a feature
    within the map to select it for editing.

    Here is how we'll implement this particular feature:

    .. image:: ./img/378-0.png
       :align: center

    OpenLayers needs to have a source of map tiles to display, so we'll create our own
    simple **Tile Map Server (TMS)** built on top of a Mapnik-based map renderer to
    display the shapefile's features stored in the database. We'll also write a simple "click
    handler" in JavaScript that intercepts clicks on the map and sends off an AJAX request
    to the server to see which feature the user clicked on. If the user does click on a feature
    (rather than just clicking on the map's background), the user's web browser will be
    redirected to the "Edit Feature" page so that the user can edit the clicked-on feature.

    There's a lot here, requiring a fair amount of custom coding, but the end result is
    a friendly interface to the ShapeEditor, allowing the user to simply point-and-click
    at a desired feature to edit it. In the process of building all this, we'll also learn how
    to use OpenLayers directly within a GeoDjango application, and how to implement
    our own Tile Map Server built on top of Mapnik.


编辑要素
-------------------------
Editing a feature

.. tab:: 中文

    To let the user edit the feature, we'll use GeoDjango's built-in geometry editing
    widget. There is a slight amount of work required here, because we want to use
    this widget outside of GeoDjango's admin interface and will need to customize
    the interface slightly.

    The only other issue that needs to be dealt with is the fact that we don't know
    in advance what type of feature we'll be editing. Shapefiles can hold any type
    of geometry, from Points and LineStrings through to MultiPolygons and
    GeometryCollections. Fortunately, all the features in a shapefile have to have
    the same geometry type, so we can store the geometry type in the Shapefile
    object, and use it to select the appropriate type of editor when editing that
    shapefile's features.

.. tab:: 英文

    To let the user edit the feature, we'll use GeoDjango's built-in geometry editing
    widget. There is a slight amount of work required here, because we want to use
    this widget outside of GeoDjango's admin interface and will need to customize
    the interface slightly.
    
    The only other issue that needs to be dealt with is the fact that we don't know
    in advance what type of feature we'll be editing. Shapefiles can hold any type
    of geometry, from Points and LineStrings through to MultiPolygons and
    GeometryCollections. Fortunately, all the features in a shapefile have to have
    the same geometry type, so we can store the geometry type in the Shapefile
    object, and use it to select the appropriate type of editor when editing that
    shapefile's features.


导出 Shapefile
-------------------------
Exporting a shapefile

.. tab:: 中文

    Exporting a shapefile involves the reverse of the "Import Shapefile" process: we have
    to create a new shapefile on disk, define the various attributes that will be stored
    in the shapefile, and then process all the features and their attributes, writing them
    out to the shapefile. Once this has been done, we can create a ZIP archive from the
    contents of the shapefile, and tell the user's web browser to download that ZIP
    archive to the user's hard disk.

.. tab:: 英文

    Exporting a shapefile involves the reverse of the "Import Shapefile" process: we have
    to create a new shapefile on disk, define the various attributes that will be stored
    in the shapefile, and then process all the features and their attributes, writing them
    out to the shapefile. Once this has been done, we can create a ZIP archive from the
    contents of the shapefile, and tell the user's web browser to download that ZIP
    archive to the user's hard disk.

