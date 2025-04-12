定义数据模型
============================================

Defining the data models

.. tab:: 中文

    我们已经知道需要哪些数据库对象来存储上传的 shapefile：

    - **Shapefile 对象** 将代表单个上传的 shapefile。
    - 每个 shapefile 将有多个 **Attribute 对象**，提供 shapefile 内每个属性的名称、数据类型和其他信息。
    - 每个 shapefile 将有多个 **Feature 对象**，保存 shapefile 每个要素的几何信息。
    - 每个要素将有一组 **AttributeValue 对象**，保存该要素每个属性的值。

    让我们更详细地看看这些对象，并思考每个对象需要存储的确切信息。

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

    当我们导入一个 shapefile 时，有几件事情需要记住：

    - 上传文件的原始名称。我们将在“列出 shapefile”视图中显示此名称，以便用户可以在该列表中识别 shapefile。
    - shapefile 数据所使用的空间参考系统。导入 shapefile 时，我们将把它转换为使用 WGS84 基准面（EPSG 4326）的纬度和经度坐标，但我们需要记住 shapefile 的原始空间参考系统，以便在导出要素时再次使用。为了简单起见，我们将以 WKT 格式存储空间参考系统。
    - shapefile 中存储的几何类型。我们需要这个信息来知道 Feature 对象中的哪个字段保存了几何信息。
    - shapefile 属性所使用的字符编码。Shapefile 并不总是采用 UTF-8 字符编码，虽然在导入数据时我们会将属性值转换为 Unicode，但我们确实需要知道文件的原始字符编码，因此我们也会将此信息存储在 shapefile 中。这允许我们在再次导出 shapefile 时使用相同的字符编码。

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

    当我们导出一个 shapefile 时，它必须具有与原始导入文件相同的属性。因此，我们必须记住 shapefile 的属性。这就是 `Attribute` 对象的作用。我们需要为每个属性记住以下信息：

    - 属性所属的 shapefile
    - 属性的名称
    - 此属性中存储的数据类型（字符串、浮点数等）
    - 属性的字段宽度（以字符为单位）
    - 对于浮点属性，小数点后显示的位数

    所有这些信息都直接来自 shapefile 的图层定义。

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

    导入的 shapefile 中的每个要素都需要存储在数据库中。由于 PostGIS（和 GeoDjango）对不同的几何类型使用不同的字段类型，我们需要为每种几何类型定义单独的字段。因此，`Feature` 对象需要存储以下信息：

    - 要素所属的 shapefile
    - 如果 shapefile 存储这种类型的几何体，则存储点几何体（Point）
    - 如果 shapefile 存储这种类型的几何体，则存储多点几何体（MultiPoint）
    - 如果 shapefile 存储这种类型的几何体，则存储多线几何体（MultiLineString）
    - 如果 shapefile 存储这种类型的几何体，则存储多边形几何体（MultiPolygon）
    - 如果 shapefile 存储这种类型的几何体，则存储几何体集合（GeometryCollection）

    .. note::

        是不是漏了点什么？

        如果您一直在关注，可能已经注意到一些几何类型缺失了。多边形（Polygons）或线串（LineStrings）呢？由于数据在 shapefile 中的存储方式，无法预先知道一个 shapefile 到底包含多边形还是多多边形，线串还是多线串。shapefile 的内部结构并不区分这些几何类型。因此，一个 shapefile 可能声称存储的是多边形，而实际上包含的是多多边形，线串也是如此。

        有关更多信息，请参见 http://code.djangoproject.com/ticket/7218。

        为了克服这个限制，我们将所有的多边形存储为多多边形，所有的线串存储为多线串。这就是为什么我们在 `Feature` 对象中不需要多边形或线串字段的原因。

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

    `AttributeValue` 对象保存每个要素属性的值。这个对象相当简单，存储以下信息：

    - 属性值所属的要素
    - 该值所属的属性
    - 属性的值（以字符串形式）

    为了简单起见，我们将所有属性值都存储为字符串。

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

    现在我们已经知道了要在数据库中存储哪些信息，因此定义各种模型对象就变得很容易。为此，请编辑 `shapeEditor.shared` 目录中的 `models.py` 文件，确保它如下所示::

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

    在这里有几个需要注意的地方：

    - 注意，顶部的 `from...import` 语句已经改变。我们导入的是 GeoDjango 模型，而不是标准的 Django 模型。
    - 我们使用 `models.CharField` 对象来表示字符数据，使用 `models.IntegerField` 对象来表示整数值。Django 提供了很多不同类型的字段供你使用。GeoDjango 还添加了自己的字段类型来存储几何字段，正如你在 `Feature` 对象的定义中看到的那样。
    - 要表示两个对象之间的关系，我们使用 `models.ForeignKey` 对象。
    - 由于 `Feature` 对象将存储几何数据，我们希望允许 GeoDjango 对这些数据进行空间查询。为了实现这一点，我们为 `Feature` 类定义了一个 `GeoManager()` 实例。
    - 注意，多个字段（特别是 `Feature` 对象中的 `geom_XXX` 字段）都同时设置了 `blank=True` 和 `null=True`。这实际上是两个不同的设置：`blank=True` 表示管理界面允许用户将该字段留空，而 `null=True` 告诉数据库这些字段可以在数据库中设置为 NULL。对于 `Feature` 对象，我们需要这两者，以避免在通过管理界面输入几何数据时出现验证错误。

    这就是我们目前需要做的（定义数据库模型）。在做出这些更改后，保存文件，进入项目的最上层目录，然后输入::

        python manage.py syncdb

    此命令会告诉 Django 检查模型并根据需要创建新的数据库表。由于新项目的默认设置自动包含了 `auth` 应用，因此系统还会询问你是否要创建超级用户账户。创建一个超级用户吧；在下一节中我们将探索 GeoDjango 的内置管理界面。

    .. note::

        Django 1.4 中有一个 bug，导致地理空间字段不会自动创建。你会看到如下错误信息::

            Failed to install index for shared.Feature model:
            operator class "gist_geometry_ops" does not exist for
            access method "gist"

        不用担心；如果你看到这个错误，你只需要手动创建空间字段。接下来我们将看到如何操作。

    现在，您应该已经设置了一个空间数据库，包含了您创建的各种数据库表。让我们通过输入以下命令来更详细地查看这个数据库::

        psql geodjango

    输入 `\d` 并按回车，您应该会看到所有已创建的数据库表的列表：

    .. csv-table:: **关系列表**
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

    为了确保每个应用的数据库表名是唯一的，Django 会在表名的开始加上应用名称。这意味着我们创建的模型的表实际上叫做 `shared_shapefile`、`shared_feature` 等等。稍后我们将直接操作这些数据库表，当我们想使用 Mapnik 生成使用导入的 Shapefile 数据的地图时。如果你遇到了防止 Django 创建空间字段的 bug，你可以通过输入以下 SQL 命令手动创建它们：

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

