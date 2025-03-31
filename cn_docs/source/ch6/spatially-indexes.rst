空间索引
============================================

Spatial indexes

.. tab:: 中文

   空间数据库的一个重要特点是能够创建特殊的空间索引，以加速基于几何形状的搜索。这些索引用于执行空间操作，例如识别位于给定边界框内的所有要素、识别离给定点一定距离内的所有要素，或识别与给定多边形相交的所有要素。

   空间索引的定义与定义普通数据库索引的方式相同，不同之处在于添加了 **SPATIAL** 关键字，以将索引标识为空间索引。例如：

   .. code-block:: sql

      CREATE TABLE cities (
         id   INTEGER AUTO_INCREMENT PRIMARY KEY,
         name CHAR(255),
         geom POLYGON NOT NULL,

         INDEX (name),
         SPATIAL INDEX (geom))

   在本章中我们将介绍的所有三种开源空间数据库，都是使用 **R-Tree** 数据结构来实现空间索引的。

   .. note::

      PostGIS 使用 PostgreSQL 的 **Generalized Search Tree (GiST)** 索引类型来实现 R-Tree。尽管在 PostGIS 中使用 GIST 类型来定义空间索引，但它们在内部仍然作为 R-Tree 来实现。

   R-Tree 索引是空间数据库最强大的功能之一，值得花些时间了解它们的工作原理。R-Tree 使用 **最小边界矩形** 来表示每个几何图形，允许数据库通过几何图形在空间中的位置快速搜索：

   .. image:: ./img/178-0.png
      :scale: 80
      :class: with-border
      :align: center

   这些边界框根据它们之间的相对位置被分组为嵌套的层次结构：

   .. image:: ./img/178-1.png
      :scale: 40
      :class: with-border
      :align: center

   这个嵌套的边界框层次结构随后通过类似树的数据结构来表示：

   .. image:: ./img/179-0.png
      :scale: 80
      :class: with-border
      :align: center

   计算机可以通过遍历这个树结构来快速找到特定的几何图形，或者比较不同几何图形的位置和大小。例如，假设我们想找到与以下点相交的多边形：

   .. image:: ./img/179-1.png
      :scale: 40
      :class: with-border
      :align: center

   数据库可以通过遍历树并比较每一层的边界框，快速找到这个几何图形。R-Tree 将以以下方式进行搜索：

   .. image:: ./img/180-0.png
      :scale: 60
      :class: with-border
      :align: center

   使用 R-Tree 索引，仅需要三次比较就能找到所需的多边形。

   由于树结构的层次特性，R-Tree 索引的扩展性非常好，能够通过极少的边界框比较，快速搜索数以万计的要素。而且，由于每个几何图形都简化为一个简单的边界框，R-Tree 索引可以支持任何类型的几何图形，而不仅仅是多边形。

   R-Tree 索引不仅限于搜索封闭的坐标；它们可以用于各种空间比较以及空间连接。我们将在下一章中广泛使用空间索引。

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
