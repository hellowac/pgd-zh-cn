小结
============================================

Summary

.. tab:: 中文

    在本章中，我们深入探讨了将空间数据存储在数据库中的概念，并研究了三种主要的开源空间数据库。我们看到了以下内容：

    - 空间数据库不同于普通的关系型数据库，它们直接支持空间数据类型、空间查询和空间连接。
    - 空间索引通常使用 R-Tree 数据结构来表示嵌套的边界框层次结构。
    - 空间索引可以快速根据几何体在空间中的位置找到几何体，并基于其边界框执行几何体之间的空间比较。
    - **MySQL**，世界上最流行的开源数据库，内置空间功能，尽管存在一些限制。
    - **PostGIS** 被认为是空间数据库的强大动力，它建立在开源数据库引擎 PostgreSQL 之上。
    - **SpatiaLite** 是对 SQLite 无服务器数据库的扩展，内置了大量空间功能。
    - 每个数据库都有一套与地理空间数据打交道的最佳实践。
    - **MySQL** 是一个足够但有限的空间数据库，**PostGIS** 是一个复杂的工作马，扩展性强，而 **SpatiaLite** 则出人意料地强大，但有一些小问题和 bug。
    - 这三种空间数据库都足够强大，能够在复杂的现实世界地理空间应用中使用，选择哪个数据库通常取决于个人偏好和可用性。

    在下一章中，我们将探讨如何利用空间数据库来解决各种地理空间问题，并构建一个复杂的地理空间应用程序。

.. tab:: 英文

    In this chapter, we have taken an in-depth look at the concept of storing spatial data in a database, and examined three of the principal open source spatial databases. We have seen the following:

    - Spatial databases differ from ordinary relational databases as they directly support spatial data types, spatial queries, and spatial joins
    - Spatial indexes generally make use of R-Tree data structures to represent nested hierarchies of bounding boxes
    - Spatial indexes can be used to quickly find geometries based on their position in space, as well as for performing spatial comparisons between geometries based on their bounding boxes
    - MySQL, the world's most popular open source database, has spatial capabilities built in, though with some limitations
    - PostGIS is considered to be the powerhouse of spatial databases, built on top of the PostgreSQL open source database engine
    - SpatiaLite is an extension to the SQLite serverless database, with a large number of spatial capabilities built in
    - Each database has a set of best practices for working with geospatial data
    - MySQL is an adequate though limited spatial database, PostGIS is a complex workhorse that scales well, and SpatiaLite is surprisingly capable but is quirky and suffers from bugs
    - All three spatial databases are powerful enough to use in complex, real-world geospatial applications, and that the choice of which database to use often comes down to personal preference and availability

    In the next chapter, we will look at how we can use spatial databases to solve a variety of geospatial problems while building a sophisticated geospatial application.
