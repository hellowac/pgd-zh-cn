定义数据模型
============================================

Defining the data models

.. tab:: 中文

    We already know which database objects we are going to need to store the
    uploaded shapefiles:

    - The Shapefile object will represent a single uploaded shapefile.
    - Each shapefile will have a number of Attribute objects, giving the name, data type, and other information about each attribute within the shapefile.
    - Each shapefile will have a number of Feature objects, which hold the geometry for each of the shapefile's features.
    - Each feature will have a set of AttributeValue objects, which hold the value for each of the feature's attributes.
    
    Let's look at each of these in more detail, and think about exactly what information will need to be stored in each object.

.. tab:: 英文

    We already know which database objects we are going to need to store the
    uploaded shapefiles:

    - The Shapefile object will represent a single uploaded shapefile.
    - Each shapefile will have a number of Attribute objects, giving the name, data type, and other information about each attribute within the shapefile.
    - Each shapefile will have a number of Feature objects, which hold the geometry for each of the shapefile's features.
    - Each feature will have a set of AttributeValue objects, which hold the value for each of the feature's attributes.
    
    Let's look at each of these in more detail, and think about exactly what information will need to be stored in each object.

Shapefile
--------------------
Shapefile

.. tab:: 中文

    When we import a shapefile, there are a few things we need to remember:

    - The original name of the uploaded file. We will display this in the "list shapefiles" view, so that the user can identify the shapefile within this list.
    - The spatial reference system used by the shapefile's data is present. When we import the shapefile, we will convert it to use latitude and longitude coordinates using the WGS84 datum (EPSG 4326), but we need to remember the shapefile's original spatial reference system so that we can use it again when exporting the features. For simplicity, we're going to store the spatial reference system in WKT format.
    - What type of geometry was stored in the shapefile. We'll need this to know which field in the Feature object holds the geometry.
    - The character encoding used for the shapefile's attributes. Shapefiles don't always come in UTF-8 character encoding, and while we'll convert the attribute values to Unicode when importing the data, we do need to know which character encoding the file was in, so we'll store this information in the shapefile as well. This allows us to use the same character encoding when exporting the shapefile again.

.. tab:: 英文

    When we import a shapefile, there are a few things we need to remember:

    - The original name of the uploaded file. We will display this in the "list shapefiles" view, so that the user can identify the shapefile within this list.
    - The spatial reference system used by the shapefile's data is present. When we import the shapefile, we will convert it to use latitude and longitude coordinates using the WGS84 datum (EPSG 4326), but we need to remember the shapefile's original spatial reference system so that we can use it again when exporting the features. For simplicity, we're going to store the spatial reference system in WKT format.
    - What type of geometry was stored in the shapefile. We'll need this to know which field in the Feature object holds the geometry.
    - The character encoding used for the shapefile's attributes. Shapefiles don't always come in UTF-8 character encoding, and while we'll convert the attribute values to Unicode when importing the data, we do need to know which character encoding the file was in, so we'll store this information in the shapefile as well. This allows us to use the same character encoding when exporting the shapefile again.

属性
--------------------
Attribute

.. tab:: 中文

    When we export a shapefile, it has to have the same attributes as the original imported file. Because of this, we have to remember the shapefile's attributes. This is what the Attribute object does. We will need to remember the following information for each attribute:

    - The shapefile the attribute belongs to
    - The name of the attribute
    - The type of data stored in this attribute (string, floating-point number, and so on)
    - The field width of the attribute, in characters
    - For floating-point attributes, the number of digits to display after the decimal point
    
    All of this information comes directly from the shapefile's layer definition.

.. tab:: 英文

    When we export a shapefile, it has to have the same attributes as the original imported file. Because of this, we have to remember the shapefile's attributes. This is what the Attribute object does. We will need to remember the following information for each attribute:

    - The shapefile the attribute belongs to
    - The name of the attribute
    - The type of data stored in this attribute (string, floating-point number, and so on)
    - The field width of the attribute, in characters
    - For floating-point attributes, the number of digits to display after the decimal point
    
    All of this information comes directly from the shapefile's layer definition.


特征
--------------------
Feature

.. tab:: 中文

    Each feature in the imported shapefile will need to be stored in the database. Because
    PostGIS (and GeoDjango) uses different field types for different types of geometries,
    we need to define separate fields for each geometry type. Because of this, the
    Feature object will need to store the following information:

    - The shapefile the feature belongs to
    - The Point geometry, if the shapefile stores this type of geometry
    - The MultiPoint geometry, if the shapefile stores this type of geometry
    - The MultLineString geometry, if the shapefile stores this type of geometry
    - The MultiPolygon geometry, if the shapefile stores this type of geometry
    - The GeometryCollection geometry, if the shapefile stores this type of geometry

    .. note::

        Isn't something missing?

        If you've been paying attention, you've probably noticed that some of
        the geometry types are missing. What about Polygons or LineStrings?
        Because of the way data is stored in a shapefile, it is impossible to know
        in advance whether a shapefile holds Polygons or MultiPolygons, and
        similarly if it holds LineStrings or MultiLineStrings. The shapefile's
        internal structure makes no distinction between these geometry types.
        Because of this, a shapefile may claim to store Polygons when it really
        contains MultiPolygons, and similarly for LineString geometries.
        For more information, see http://code.djangoproject.com/ticket/7218.
        
        To work around this limitation, we store all Polygons as MultiPolygons,
        and all LineStrings as MultiLineStrings. This is why we don't need
        Polygon or LineString fields in the Feature object.

.. tab:: 英文

    Each feature in the imported shapefile will need to be stored in the database. Because
    PostGIS (and GeoDjango) uses different field types for different types of geometries,
    we need to define separate fields for each geometry type. Because of this, the
    Feature object will need to store the following information:

    - The shapefile the feature belongs to
    - The Point geometry, if the shapefile stores this type of geometry
    - The MultiPoint geometry, if the shapefile stores this type of geometry
    - The MultLineString geometry, if the shapefile stores this type of geometry
    - The MultiPolygon geometry, if the shapefile stores this type of geometry
    - The GeometryCollection geometry, if the shapefile stores this type of geometry

    .. note::

        Isn't something missing?

        If you've been paying attention, you've probably noticed that some of
        the geometry types are missing. What about Polygons or LineStrings?
        Because of the way data is stored in a shapefile, it is impossible to know
        in advance whether a shapefile holds Polygons or MultiPolygons, and
        similarly if it holds LineStrings or MultiLineStrings. The shapefile's
        internal structure makes no distinction between these geometry types.
        Because of this, a shapefile may claim to store Polygons when it really
        contains MultiPolygons, and similarly for LineString geometries.
        For more information, see http://code.djangoproject.com/ticket/7218.
        
        To work around this limitation, we store all Polygons as MultiPolygons,
        and all LineStrings as MultiLineStrings. This is why we don't need
        Polygon or LineString fields in the Feature object.

属性值
--------------------
AttributeValue

.. tab:: 中文

    The AttributeValue object holds the value for each of the feature's attributes.
    This object is quite straightforward, storing the following information:

    - The feature the attribute value is for
    - The attribute this value is for
    - The attribute's value, as a string
    
    For simplicity, we'll be storing all attribute values as strings.

.. tab:: 英文

    The AttributeValue object holds the value for each of the feature's attributes.
    This object is quite straightforward, storing the following information:

    - The feature the attribute value is for
    - The attribute this value is for
    - The attribute's value, as a string
    
    For simplicity, we'll be storing all attribute values as strings.


models.py 文件
--------------------
The models.py file

.. tab:: 中文

    Now that we know what information we want to store in our database, it's easy
    to define our various model objects. To do this, edit the models.py file in the
    shapeEditor.shared directory, and make sure it looks like this::

        from django.contrib.gis.db import models

        class Shapefile(models.Model):
            filename  = models.CharField(max_length=255)
            srs_wkt   = models.CharField(max_length=255)
            geom_type = models.CharField(max_length=50)
            encoding  = models.CharField(max_length=20)
            
        class Attribute(models.Model):
            shapefile = models.ForeignKey(Shapefile)
            name      = models.CharField(max_length=255)
            type      = models.IntegerField()
            width     = models.IntegerField()
            precision = models.IntegerField()

        class Feature(models.Model):
            shapefile  = models.ForeignKey(Shapefile)
            geom_point = models.PointField(srid=4326,
                                           blank=True, null=True)
            geom_multipoint = \
                        models.MultiPointField(srid=4326,
                                               blank=True, null=True)
            geom_multilinestring = \
                        models.MultiLineStringField(srid=4326,
                                               blank=True, null=True)
            geom_multipolygon = \
                        models.MultiPolygonField(srid=4326,
                                               blank=True, null=True)
            geom_geometrycollection = \
                        models.GeometryCollectionField(srid=4326,
                                                       blank=True,
                                                       null=True)
            objects = models.GeoManager()

            class AttributeValue(models.Model):
                feature   = models.ForeignKey(Feature)
                attribute = models.ForeignKey(Attribute)
                value     = models.CharField(max_length=255,
                                            blank=True, null=True)

    There are a few things to be aware of here:

    - Note that the from...import statement at the top has changed. We're importing the GeoDjango models, rather than the standard Django ones.
    - We use models.CharField objects to represent character data, and models. IntegerField objects to represent integer values. Django provides a whole raft of field types for you to use. GeoDjango also adds its own field types to store geometry fields, as you can see from the definition of the Feature object.
    - To represent relations between two objects, we use a models.ForeignKey object.
    - Because the Feature object will store geometry data, we want to allow GeoDjango to perform spatial queries on this data. To enable this, we define a GeoManager() instance for the Feature class.
    - Note that several fields (in particular, the geom_XXX fields in the Feature object) have both blank=True and null=True. These are actually quite distinct: blank=True means that the admin interface allows the user to leave the field blank, while null=True tells the database that these fields can be set to NULL in the database. For the Feature object, we'll need both so that we don't get validation errors when entering geometries via the admin interface.

    That's all we need to do (for now) to define our database model. After you've made
    these changes, save the file, cd into the topmost project directory, and type::
    
        python manage.py syncdb
    
    This command tells Django to check the models and create new database tables as
    required. Because the default settings for a new project automatically include the
    auth application, you will also be asked if you want to create a superuser account.
    Go ahead and create one; we'll need a superuser for the next section, where we
    explore GeoDjango's built-in admin interface.

    .. note::

        There is bug in Django 1.4, which means that geospatial fields aren't
        created automatically. Instead, you'll see the following error message::
        
            Failed to install index for shared.Feature model:
            operator class "gist_geometry_ops" does not exist for
            access method "gist"

        Don't worry; if you see this error, you just need to create the spatial
        fields by hand. We are about to see how to do this.

    You should now have a spatial database set up with the various database tables you have created. Let's take a closer look at this database by typing::

        psql geodjango

    When you type \d and press Return, you should see a list of all the database tables that have been created:

    .. csv-table:: **List of relations**
       :header: "Schema", "Name", "Type", "Owner"

       "public", "auth_group", "table", "user"
       "public", "auth_group_id_seq", "sequence", "user"
       "public", "auth_group_permissions", "table", "user"
       "public", "auth_group_permissions_id_seq", "sequence", "user"
       "public", "auth_message", "table", "user"
       "public", "auth_message_id_seq", "sequence", "user"
       "public", "auth_permission", "table", "user"
       "public", "auth_permission_id_seq", "sequence", "user"
       "public", "auth_user", "table", "user"
       "public", "auth_user_groups", "table", "user"
       "public", "auth_user_groups_id_seq", "sequence", "user"
       "public", "auth_user_id_seq", "sequence", "user"
       "public", "auth_user_user_permissions", "table", "user"
       "public", "auth_user_user_permissions_id_seq", "sequence", "user"
       "public", "django_content_type", "table", "user"
       "public", "django_content_type_id_seq", "sequence", "user"
       "public", "django_session", "table", "user"
       "public", "django_site", "table", "user"
       "public", "django_site_id_seq", "sequence", "user"
       "public", "geography_columns", "view", "user"
       "public", "geometry_columns", "view", "user"
       "public", "raster_columns", "view", "user"
       "public", "raster_overviews", "view", "user"
       "public", "shared_attribute", "table", "user"
       "public", "shared_attribute_id_seq", "sequence", "user"
       "public", "shared_attributevalue", "table", "user"
       "public", "shared_attributevalue_id_seq", "sequence", "user"
       "public", "shared_feature", "table", "user"
       "public", "shared_feature_id_seq", "sequence", "user"
       "public", "shared_shapefile", "table", "user"
       "public", "shared_shapefile_id_seq", "sequence", "user"
       "public", "spatial_ref_sys", "table", "user"
       "(30 rows)" 

    To make sure that each application's database tables are unique, Django adds the application name to the start of the table name. This means that the table names for the models we have created are actually called shared_shapefile, shared_feature, and so on. We'll be working with these database tables directly later on, when we want to use Mapnik to generate maps using the imported Shapefile data. If you ran into the bug that prevents Django from creating the spatial fields, you can create them yourself by typing the following commands into pgsql:

    .. code-block:: sql

        ALTER TABLE shared_feature ADD COLUMN geom_point
            geometry(Point, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_multipoint
            geometry(MultiPoint, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_
            multilinestring geometry(MultiLineString, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_multipolygon
            geometry(MultiPolygon, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_
            geometrycollection geometry(GeometryCollection, 4326);

.. tab:: 英文

    Now that we know what information we want to store in our database, it's easy
    to define our various model objects. To do this, edit the models.py file in the
    shapeEditor.shared directory, and make sure it looks like this::

        from django.contrib.gis.db import models

        class Shapefile(models.Model):
            filename  = models.CharField(max_length=255)
            srs_wkt   = models.CharField(max_length=255)
            geom_type = models.CharField(max_length=50)
            encoding  = models.CharField(max_length=20)
            
        class Attribute(models.Model):
            shapefile = models.ForeignKey(Shapefile)
            name      = models.CharField(max_length=255)
            type      = models.IntegerField()
            width     = models.IntegerField()
            precision = models.IntegerField()

        class Feature(models.Model):
            shapefile  = models.ForeignKey(Shapefile)
            geom_point = models.PointField(srid=4326,
                                           blank=True, null=True)
            geom_multipoint = \
                        models.MultiPointField(srid=4326,
                                               blank=True, null=True)
            geom_multilinestring = \
                        models.MultiLineStringField(srid=4326,
                                               blank=True, null=True)
            geom_multipolygon = \
                        models.MultiPolygonField(srid=4326,
                                               blank=True, null=True)
            geom_geometrycollection = \
                        models.GeometryCollectionField(srid=4326,
                                                       blank=True,
                                                       null=True)
            objects = models.GeoManager()

            class AttributeValue(models.Model):
                feature   = models.ForeignKey(Feature)
                attribute = models.ForeignKey(Attribute)
                value     = models.CharField(max_length=255,
                                            blank=True, null=True)

    There are a few things to be aware of here:

    - Note that the from...import statement at the top has changed. We're importing the GeoDjango models, rather than the standard Django ones.
    - We use models.CharField objects to represent character data, and models. IntegerField objects to represent integer values. Django provides a whole raft of field types for you to use. GeoDjango also adds its own field types to store geometry fields, as you can see from the definition of the Feature object.
    - To represent relations between two objects, we use a models.ForeignKey object.
    - Because the Feature object will store geometry data, we want to allow GeoDjango to perform spatial queries on this data. To enable this, we define a GeoManager() instance for the Feature class.
    - Note that several fields (in particular, the geom_XXX fields in the Feature object) have both blank=True and null=True. These are actually quite distinct: blank=True means that the admin interface allows the user to leave the field blank, while null=True tells the database that these fields can be set to NULL in the database. For the Feature object, we'll need both so that we don't get validation errors when entering geometries via the admin interface.

    That's all we need to do (for now) to define our database model. After you've made
    these changes, save the file, cd into the topmost project directory, and type::
    
        python manage.py syncdb
    
    This command tells Django to check the models and create new database tables as
    required. Because the default settings for a new project automatically include the
    auth application, you will also be asked if you want to create a superuser account.
    Go ahead and create one; we'll need a superuser for the next section, where we
    explore GeoDjango's built-in admin interface.

    .. note::

        There is bug in Django 1.4, which means that geospatial fields aren't
        created automatically. Instead, you'll see the following error message::
        
            Failed to install index for shared.Feature model:
            operator class "gist_geometry_ops" does not exist for
            access method "gist"

        Don't worry; if you see this error, you just need to create the spatial
        fields by hand. We are about to see how to do this.

    You should now have a spatial database set up with the various database tables you have created. Let's take a closer look at this database by typing::

        psql geodjango

    When you type \d and press Return, you should see a list of all the database tables that have been created:

    .. csv-table:: **List of relations**
       :header: "Schema", "Name", "Type", "Owner"

       "public", "auth_group", "table", "user"
       "public", "auth_group_id_seq", "sequence", "user"
       "public", "auth_group_permissions", "table", "user"
       "public", "auth_group_permissions_id_seq", "sequence", "user"
       "public", "auth_message", "table", "user"
       "public", "auth_message_id_seq", "sequence", "user"
       "public", "auth_permission", "table", "user"
       "public", "auth_permission_id_seq", "sequence", "user"
       "public", "auth_user", "table", "user"
       "public", "auth_user_groups", "table", "user"
       "public", "auth_user_groups_id_seq", "sequence", "user"
       "public", "auth_user_id_seq", "sequence", "user"
       "public", "auth_user_user_permissions", "table", "user"
       "public", "auth_user_user_permissions_id_seq", "sequence", "user"
       "public", "django_content_type", "table", "user"
       "public", "django_content_type_id_seq", "sequence", "user"
       "public", "django_session", "table", "user"
       "public", "django_site", "table", "user"
       "public", "django_site_id_seq", "sequence", "user"
       "public", "geography_columns", "view", "user"
       "public", "geometry_columns", "view", "user"
       "public", "raster_columns", "view", "user"
       "public", "raster_overviews", "view", "user"
       "public", "shared_attribute", "table", "user"
       "public", "shared_attribute_id_seq", "sequence", "user"
       "public", "shared_attributevalue", "table", "user"
       "public", "shared_attributevalue_id_seq", "sequence", "user"
       "public", "shared_feature", "table", "user"
       "public", "shared_feature_id_seq", "sequence", "user"
       "public", "shared_shapefile", "table", "user"
       "public", "shared_shapefile_id_seq", "sequence", "user"
       "public", "spatial_ref_sys", "table", "user"
       "(30 rows)" 

    To make sure that each application's database tables are unique, Django adds the application name to the start of the table name. This means that the table names for the models we have created are actually called shared_shapefile, shared_feature, and so on. We'll be working with these database tables directly later on, when we want to use Mapnik to generate maps using the imported Shapefile data. If you ran into the bug that prevents Django from creating the spatial fields, you can create them yourself by typing the following commands into pgsql:

    .. code-block:: sql

        ALTER TABLE shared_feature ADD COLUMN geom_point
            geometry(Point, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_multipoint
            geometry(MultiPoint, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_
            multilinestring geometry(MultiLineString, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_multipolygon
            geometry(MultiPolygon, 4326);
        ALTER TABLE shared_feature ADD COLUMN geom_
            geometrycollection geometry(GeometryCollection, 4326);

