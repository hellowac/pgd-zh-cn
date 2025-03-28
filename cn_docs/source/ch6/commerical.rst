商业空间数据库
============================================

Commercial Spatially-enabled databases

.. tab:: 中文

    While we will be concentrating on the use of open source databases in this book, it is worth spending a moment exploring the commercial alternatives. There are two major commercial databases which support spatial operations: Oracle and Microsoft's SQL Server.

.. tab:: 英文

    While we will be concentrating on the use of open source databases in this book, it is worth spending a moment exploring the commercial alternatives. There are two major commercial databases which support spatial operations: Oracle and Microsoft's SQL Server.

Oracle
--------------
Oracle

.. tab:: 中文

    Oracle provides one of the world's most powerful and popular commercial database systems. Spatial extensions to the **Oracle** database are available in two flavors. Oracle Spatial provides a large range of geospatial database features, including spatial data types, spatial indexes, the ability to perform spatial queries and joins, and a range of spatial functions. Oracle Spatial also supports linear referencing systems, spatial analysis, and data-mining functions, geocoding, and support for raster-format data.

    While Oracle Spatial is only available for the Enterprise edition of the Oracle database, it is one of the most powerful spatially-enabled databases available anywhere.

    A subset of the Oracle Spatial functionality, called **Oracle Locator**, is available for the Standard edition of the Oracle database. Oracle Locator does not support common operations such as unions and buffers, intersections, area and length calculations. It also excludes support for more advanced features such as linear referencing systems, spatial analysis functions, geocoding, and raster format data.

    While being extremely capable, Oracle does have the disadvantage of using a somewhat non-standard syntax compared with other SQL databases. It also uses non-standard function names for its spatial extensions, making it difficult to switch database engines or use examples written for other databases.

.. tab:: 英文

    Oracle provides one of the world's most powerful and popular commercial database systems. Spatial extensions to the **Oracle** database are available in two flavors. Oracle Spatial provides a large range of geospatial database features, including spatial data types, spatial indexes, the ability to perform spatial queries and joins, and a range of spatial functions. Oracle Spatial also supports linear referencing systems, spatial analysis, and data-mining functions, geocoding, and support for raster-format data.

    While Oracle Spatial is only available for the Enterprise edition of the Oracle database, it is one of the most powerful spatially-enabled databases available anywhere.

    A subset of the Oracle Spatial functionality, called **Oracle Locator**, is available for the Standard edition of the Oracle database. Oracle Locator does not support common operations such as unions and buffers, intersections, area and length calculations. It also excludes support for more advanced features such as linear referencing systems, spatial analysis functions, geocoding, and raster format data.

    While being extremely capable, Oracle does have the disadvantage of using a somewhat non-standard syntax compared with other SQL databases. It also uses non-standard function names for its spatial extensions, making it difficult to switch database engines or use examples written for other databases.


MS SQL Server
--------------
MS SQL Server

.. tab:: 中文

    Microsoft's SQL Server is another widely-used and powerful commercial database system. SQL Server supports a full range of geospatial operations, including support for both geometry and geography data types, and all of the standard geospatial functions and operators.

    Because Microsoft has followed the Open Geospatial Consortium's standards, the data types and function names used by SQL Server match those used by the open source databases we have already examined. The only difference stems from SQL Server's own internal object oriented nature; for example, rather than *ST_Intersects(geom, pt)*, SQL Server uses geom.*STIntersects(pt)*.

    Unlike Oracle, all of Microsoft's spatial extensions are included in every edition of the SQL Server; there is no need to obtain the Enterprise edition to get the full range of spatial capabilities.

    There are two limitations with MS SQL Server that may limit its usefulness as a spatially-enabled database. Firstly, SQL Server only runs on Microsoft Windows based computers. This limits the range of servers it can be installed on. Also, SQL Server does not support transforming data from one spatial reference system to another.

.. tab:: 英文

    Microsoft's SQL Server is another widely-used and powerful commercial database system. SQL Server supports a full range of geospatial operations, including support for both geometry and geography data types, and all of the standard geospatial functions and operators.

    Because Microsoft has followed the Open Geospatial Consortium's standards, the data types and function names used by SQL Server match those used by the open source databases we have already examined. The only difference stems from SQL Server's own internal object oriented nature; for example, rather than *ST_Intersects(geom, pt)*, SQL Server uses geom.*STIntersects(pt)*.

    Unlike Oracle, all of Microsoft's spatial extensions are included in every edition of the SQL Server; there is no need to obtain the Enterprise edition to get the full range of spatial capabilities.

    There are two limitations with MS SQL Server that may limit its usefulness as a spatially-enabled database. Firstly, SQL Server only runs on Microsoft Windows based computers. This limits the range of servers it can be installed on. Also, SQL Server does not support transforming data from one spatial reference system to another.
