空间索引
============================================

Spatial indexes

.. tab:: 中文

    One of the defining characteristics of a spatial database is the ability to create special spatial indexes to speed up geometry-based searches. These indexes are used to perform spatial operations such as identifying all the features that lie within a given bounding box, identifying all the features within a certain distance of a given point, or identifying all the features that intersect with a given polygon.

    A spatial index is defined in the same way as you define an ordinary database index, except that you add the SPATIAL keyword to identify the index as a spatial index. For example:

    .. code-block:: sql

        CREATE TABLE cities (
            id   INTEGER AUTO_INCREMENT PRIMARY KEY,
            name CHAR(255),
            geom POLYGON NOT NULL,

            INDEX (name),
            SPATIAL INDEX (geom))

    All three open source spatial databases we will examine in this chapter implement spatial indexes using **R-Tree** data structures.

    .. note::
        
        PostGIS implements R-Trees using PostgreSQL's **Generalized Search Tree (GiST)** index type. Even though you define your spatial indexes in PostGIS using the GIST type, they are still implemented as R-Trees internally.

    R-Tree indexes are one of the most powerful features of spatial databases, and it is worth spending a moment becoming familiar with how they work. R-Trees use the **minimum bounding rectangle** for each geometry to allow the database to quickly search through the geometries using their position in space:

    .. image:: ./img/178-0.png
       :scale: 80
       :class: with-border
       :align: center

    These bounding boxes are grouped into a nested hierarchy based on how close together they are:

    .. image:: ./img/178-1.png
       :scale: 40
       :class: with-border
       :align: center

    This hierarchy of nested bounding boxes is then represented using a tree-like data structure:

    .. image:: ./img/179-0.png
       :scale: 80
       :class: with-border
       :align: center

    The computer can quickly scan through this tree to find a particular geometry, or to compare the positions or sizes of the various geometries. For example, imagine that we want to find the polygon that intersects the following point:

    .. image:: ./img/179-1.png
       :scale: 40
       :class: with-border
       :align: center

    The database can quickly find this geometry by traversing the tree and comparing the bounding boxes at each level. The R-Tree will be searched in the following manner:

    .. image:: ./img/180-0.png
       :scale: 60
       :class: with-border
       :align: center

    Using the R-Tree index, it took just three comparisons to find the desired polygon.

    Because of the hierarchical nature of the tree structure, R-Tree indexes scale extremely well, and can search through many tens of thousands of features using only a handful of bounding box comparisons. And because every geometry is reduced to a simple bounding box, R-Trees can support any type of geometry, not just polygons.

    R-Tree indexes are not limited to only searching for enclosed coordinates; they can be used for all sorts of spatial comparisons, and for spatial joins. We will be working with spatial indexes extensively in the next chapter.

.. tab:: 英文

    One of the defining characteristics of a spatial database is the ability to create special spatial indexes to speed up geometry-based searches. These indexes are used to perform spatial operations such as identifying all the features that lie within a given bounding box, identifying all the features within a certain distance of a given point, or identifying all the features that intersect with a given polygon.

    A spatial index is defined in the same way as you define an ordinary database index, except that you add the SPATIAL keyword to identify the index as a spatial index. For example:

    .. code-block:: sql

        CREATE TABLE cities (
            id   INTEGER AUTO_INCREMENT PRIMARY KEY,
            name CHAR(255),
            geom POLYGON NOT NULL,

            INDEX (name),
            SPATIAL INDEX (geom))

    All three open source spatial databases we will examine in this chapter implement spatial indexes using **R-Tree** data structures.

    .. note::
        
        PostGIS implements R-Trees using PostgreSQL's **Generalized Search Tree (GiST)** index type. Even though you define your spatial indexes in PostGIS using the GIST type, they are still implemented as R-Trees internally.

    R-Tree indexes are one of the most powerful features of spatial databases, and it is worth spending a moment becoming familiar with how they work. R-Trees use the **minimum bounding rectangle** for each geometry to allow the database to quickly search through the geometries using their position in space:

    .. image:: ./img/178-0.png
       :scale: 80
       :class: with-border
       :align: center

    These bounding boxes are grouped into a nested hierarchy based on how close together they are:

    .. image:: ./img/178-1.png
       :scale: 40
       :class: with-border
       :align: center

    This hierarchy of nested bounding boxes is then represented using a tree-like data structure:

    .. image:: ./img/179-0.png
       :scale: 80
       :class: with-border
       :align: center

    The computer can quickly scan through this tree to find a particular geometry, or to compare the positions or sizes of the various geometries. For example, imagine that we want to find the polygon that intersects the following point:

    .. image:: ./img/179-1.png
       :scale: 40
       :class: with-border
       :align: center

    The database can quickly find this geometry by traversing the tree and comparing the bounding boxes at each level. The R-Tree will be searched in the following manner:

    .. image:: ./img/180-0.png
       :scale: 60
       :class: with-border
       :align: center

    Using the R-Tree index, it took just three comparisons to find the desired polygon.

    Because of the hierarchical nature of the tree structure, R-Tree indexes scale extremely well, and can search through many tens of thousands of features using only a handful of bounding box comparisons. And because every geometry is reduced to a simple bounding box, R-Trees can support any type of geometry, not just polygons.

    R-Tree indexes are not limited to only searching for enclosed coordinates; they can be used for all sorts of spatial comparisons, and for spatial joins. We will be working with spatial indexes extensively in the next chapter.
