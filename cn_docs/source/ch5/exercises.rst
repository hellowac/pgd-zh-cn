练习
============================================

Exercises

.. tab:: 中文

    If you are interested in exploring the techniques used in this chapter further, you might like to challenge yourself with the following tasks:

    - Change the "Calculate Bounding Box" calculation to exclude outlying islands.

    .. hint:: 

        You can split each country's MultiPolygon into individual Polygon objects, and then check the area of each polygon to exclude those which are smaller than a given total value.

    - Use the World Borders Dataset to create a new shapefile, where each country is represented by a single "Point" geometry containing the geographical center of each country.

    .. hint:: 

        You can start with the country bounding boxes we calculated earlier, and then calculate the midpoint using:

        .. code-block:: python

            midLat = (minLat + maxLat) / 2
            midLong = (minLong + maxLong) / 2

        For an extra challenge, you could use Shapely's centroid() method to calculate a more accurate representation of each country's center. To do this, you would have to convert the country's outline into a Shapely geometry, calculate the centroid, and then convert the centroid back into an OGR geometry before saving it into the output shapefile.

    - Extend the preceding histogram example to only include height values that fall inside a selected country's outline.

    .. hint:: 

        Implementing this in an efficient way can be difficult. A good approach would be to identify the bounding box for each of the polygons that make up the country's outline, and then iterate over the DEM coordinates within that bounding box. You could then check to see if a given coordinate is actually inside the country's outline using polygon.contains(point), and only add the height to the histogram if the point is indeed within the country's outline.

    - Optimize the "identify nearby parks" example given earlier so that it can work quickly with larger data sets.

    .. hint:: 

        One possibility might be to calculate the rectangular bounding box around each urban area, and then expand that bounding box north, south, east, and west by the desired angular distance. You could then quickly exclude all the points which aren't in that bounding box before making the time-consuming call to polygon.contains(point).

    - Calculate the total length of the coastline of the United Kingdom.

    .. hint:: 

        Remember that a country outline is a MultiPolygon, where each Polygon in the MultiPolygon represents a single island. You will need to extract the exterior ring from each of these individual island polygons, and calculate the total length of the line segments within that exterior ring. You can then total the length of each individual island to get the length of the entire country's coastline.

    - Design your own reusable library of geospatial functions which build on OGR, GDAL, Shapely, and pyproj to perform common operations such as those discussed in this chapter.

    .. hint:: 

        Writing your own reusable library modules is a common programming tactic. Think about the various tasks we have solved in this chapter, and how they can be turned into generic library functions. For example, you might like to write a function named calcLineStringLength() which takes a LineString and returns the total length of the LineString's segments, optionally transforming the LineString's coordinates into lat/long values before calling geod.inv().

        You could then write a calcPolygonOutlineLength() function which uses calcLineStringLength() to calculate the length of a polygon's outer ring.

.. tab:: 英文

    If you are interested in exploring the techniques used in this chapter further, you might like to challenge yourself with the following tasks:

    - Change the "Calculate Bounding Box" calculation to exclude outlying islands.

    .. hint:: 

        You can split each country's MultiPolygon into individual Polygon objects, and then check the area of each polygon to exclude those which are smaller than a given total value.

    - Use the World Borders Dataset to create a new shapefile, where each country is represented by a single "Point" geometry containing the geographical center of each country.

    .. hint:: 

        You can start with the country bounding boxes we calculated earlier, and then calculate the midpoint using:

        .. code-block:: python

            midLat = (minLat + maxLat) / 2
            midLong = (minLong + maxLong) / 2

        For an extra challenge, you could use Shapely's centroid() method to calculate a more accurate representation of each country's center. To do this, you would have to convert the country's outline into a Shapely geometry, calculate the centroid, and then convert the centroid back into an OGR geometry before saving it into the output shapefile.

    - Extend the preceding histogram example to only include height values that fall inside a selected country's outline.

    .. hint:: 

        Implementing this in an efficient way can be difficult. A good approach would be to identify the bounding box for each of the polygons that make up the country's outline, and then iterate over the DEM coordinates within that bounding box. You could then check to see if a given coordinate is actually inside the country's outline using polygon.contains(point), and only add the height to the histogram if the point is indeed within the country's outline.

    - Optimize the "identify nearby parks" example given earlier so that it can work quickly with larger data sets.

    .. hint:: 

        One possibility might be to calculate the rectangular bounding box around each urban area, and then expand that bounding box north, south, east, and west by the desired angular distance. You could then quickly exclude all the points which aren't in that bounding box before making the time-consuming call to polygon.contains(point).

    - Calculate the total length of the coastline of the United Kingdom.

    .. hint:: 

        Remember that a country outline is a MultiPolygon, where each Polygon in the MultiPolygon represents a single island. You will need to extract the exterior ring from each of these individual island polygons, and calculate the total length of the line segments within that exterior ring. You can then total the length of each individual island to get the length of the entire country's coastline.

    - Design your own reusable library of geospatial functions which build on OGR, GDAL, Shapely, and pyproj to perform common operations such as those discussed in this chapter.

    .. hint:: 

        Writing your own reusable library modules is a common programming tactic. Think about the various tasks we have solved in this chapter, and how they can be turned into generic library functions. For example, you might like to write a function named calcLineStringLength() which takes a LineString and returns the total length of the LineString's segments, optionally transforming the LineString's coordinates into lat/long values before calling geod.inv().

        You could then write a calcPolygonOutlineLength() function which uses calcLineStringLength() to calculate the length of a polygon's outer ring.