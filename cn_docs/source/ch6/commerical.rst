商业空间数据库
============================================

Commercial Spatially-enabled databases

.. tab:: 中文

    虽然本书将重点介绍开源数据库的使用，但值得花一些时间探索商业替代方案。目前有两种主要的商业数据库支持空间操作：Oracle 和 Microsoft 的 SQL Server。

.. tab:: 英文

    While we will be concentrating on the use of open source databases in this book, it is worth spending a moment exploring the commercial alternatives. There are two major commercial databases which support spatial operations: Oracle and Microsoft's SQL Server.

Oracle
--------------
Oracle

.. tab:: 中文

    Oracle 提供了全球最强大且最受欢迎的商业数据库系统之一。Oracle 数据库的空间扩展有两种版本可供选择。 **Oracle Spatial** 提供了大量的地理空间数据库功能，包括空间数据类型、空间索引、执行空间查询和连接的能力，以及一系列空间函数。Oracle Spatial 还支持线性参考系统、空间分析、数据挖掘功能、地理编码和对栅格数据格式的支持。

    尽管 Oracle Spatial 仅适用于 Oracle 数据库的企业版，但它仍然是世界上最强大的空间支持数据库之一。

    **Oracle Locator** 是 Oracle Spatial 功能的一个子集，适用于 Oracle 数据库的标准版。Oracle Locator 不支持常见的操作，如联合、缓冲区、交集、面积和长度计算。它还排除了对更高级功能的支持，如线性参考系统、空间分析函数、地理编码和栅格数据格式。

    尽管 Oracle 功能强大，但它有一个缺点，那就是与其他 SQL 数据库相比，Oracle 使用的语法有些不标准。它还使用了非标准的空间扩展函数名称，这使得切换数据库引擎或使用为其他数据库编写的示例变得困难。

.. tab:: 英文

    Oracle provides one of the world's most powerful and popular commercial database systems. Spatial extensions to the **Oracle** database are available in two flavors. Oracle Spatial provides a large range of geospatial database features, including spatial data types, spatial indexes, the ability to perform spatial queries and joins, and a range of spatial functions. Oracle Spatial also supports linear referencing systems, spatial analysis, and data-mining functions, geocoding, and support for raster-format data.

    While Oracle Spatial is only available for the Enterprise edition of the Oracle database, it is one of the most powerful spatially-enabled databases available anywhere.

    A subset of the Oracle Spatial functionality, called **Oracle Locator**, is available for the Standard edition of the Oracle database. Oracle Locator does not support common operations such as unions and buffers, intersections, area and length calculations. It also excludes support for more advanced features such as linear referencing systems, spatial analysis functions, geocoding, and raster format data.

    While being extremely capable, Oracle does have the disadvantage of using a somewhat non-standard syntax compared with other SQL databases. It also uses non-standard function names for its spatial extensions, making it difficult to switch database engines or use examples written for other databases.


MS SQL Server
--------------
MS SQL Server

.. tab:: 中文

    微软的 SQL Server 是另一个广泛使用且功能强大的商业数据库系统。SQL Server 支持完整的地理空间操作，包括对几何（geometry）和地理（geography）数据类型的支持，以及所有标准的地理空间函数和操作符。

    由于微软遵循了开放地理空间联盟（Open Geospatial Consortium, OGC）的标准，SQL Server 使用的数据类型和函数名称与我们之前讨论的开源数据库相匹配。唯一的不同在于 SQL Server 的内部面向对象特性；例如，SQL Server 使用 `geom.*STIntersects(pt)*` 而不是 `ST_Intersects(geom, pt)`。

    与 Oracle 不同的是，微软的所有空间扩展都包含在 SQL Server 的每个版本中；无需获取企业版就能享受完整的空间功能。

    然而，MS SQL Server 存在两个可能限制其作为空间启用数据库使用的缺点。首先，SQL Server 仅能在微软 Windows 操作系统上运行，这限制了其可安装的服务器范围。其次，SQL Server 不支持将数据从一个空间参考系统转换到另一个空间参考系统。

.. tab:: 英文

    Microsoft's SQL Server is another widely-used and powerful commercial database system. SQL Server supports a full range of geospatial operations, including support for both geometry and geography data types, and all of the standard geospatial functions and operators.

    Because Microsoft has followed the Open Geospatial Consortium's standards, the data types and function names used by SQL Server match those used by the open source databases we have already examined. The only difference stems from SQL Server's own internal object oriented nature; for example, rather than *ST_Intersects(geom, pt)*, SQL Server uses geom.*STIntersects(pt)*.

    Unlike Oracle, all of Microsoft's spatial extensions are included in every edition of the SQL Server; there is no need to obtain the Enterprise edition to get the full range of spatial capabilities.

    There are two limitations with MS SQL Server that may limit its usefulness as a spatially-enabled database. Firstly, SQL Server only runs on Microsoft Windows based computers. This limits the range of servers it can be installed on. Also, SQL Server does not support transforming data from one spatial reference system to another.
