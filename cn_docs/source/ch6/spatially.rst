空间数据库
============================================

Spatially-enabled databases

.. tab:: 中文

    从某种意义上说，几乎任何数据库都可以用来存储地理空间数据：只需将几何对象转换为 WKT 格式，并将结果存储在文本列中。但尽管这样可以将地理空间数据存储在数据库中，它却无法让你以任何有用的方式查询这些数据。你唯一能做的就是逐条记录地检索原始的 WKT 文本，并将其转换回几何对象。

    另一方面，空间支持数据库能够理解“空间”的概念，允许你直接操作空间对象和概念。特别是，空间支持数据库允许你执行以下操作：

    - 将 **空间数据类型** （点、线、面等）直接存储在数据库中，以几何列的形式。
    - 在数据上执行**空间查询**。例如：

        .. code-block:: sql

            select all landmarks within 10 km of the city named "San Francisco"

    - 在数据上执行 **空间连接** 。例如：

        .. code-block:: sql

            select all cities and their associated countries by joining cities
            and countries on (city inside country).

    - 使用各种 **空间函数** 创建新的空间对象。例如：

        .. code-block:: sql

            set "danger_zone" to the intersection of the "flooded_area" and
            "urban_area" polygons.

    如你所想，空间支持数据库是处理地理空间数据的极其强大的工具。通过使用**空间索引**和其他优化，空间数据库能够快速执行这些类型的操作，并且能够扩展以支持庞大的数据量，这在其他数据存储方案中是无法实现的。

.. tab:: 英文

    In a sense, almost any database can be used to store geospatial data: simply convert a geometry to WKT format and store the results in a text column. But while this would allow you to store geospatial data in a database, it wouldn't let you query it in any useful way. All you could do is retrieve the raw WKT text and convert it back to a geometry object, one record at a time.

    A spatially-enabled database, on the other hand, is aware of the notion of space, and allows you to work with spatial objects and concepts directly. In particular, a spatially-enabled database allows you to do the following:

    - Store **spatial datatypes** (points, lines, polygons, and so on) directly in the database, in the form of a geometry column.
    - Perform **spatial queries** on your data. For example: 

    .. code-block:: sql
    
        select all landmarks within 10 km of the city named "San Francisco"

    - Perform **spatial joins** on your data. For example:

    .. code-block:: sql
        
        select all cities and their associated countries by joining cities
        and countries on (city inside country).

    - Create new spatial objects using various **spatial functions**. For example:

    .. code-block:: sql
        
        set "danger_zone" to the intersection of the "flooded_area" and
        "urban_area" polygons.

    As you can imagine, a spatially-enabled database is an extremely powerful tool for working with geospatial data. By using **spatial indexes** and other optimizations, spatial databases can quickly perform these types of operations, and can scale to support vast amounts of data simply not feasible using other data-storage schemes.
