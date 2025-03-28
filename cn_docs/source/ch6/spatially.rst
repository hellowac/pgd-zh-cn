空间数据库
============================================

Spatially-enabled databases

.. tab:: 中文

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
