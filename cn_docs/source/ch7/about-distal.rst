关于 DISTAL
============================================

About DISTAL

.. tab:: 中文

    The DISTAL application will have the following basic workflow:

    1. The user starts by selecting the country they wish to work with:

    .. image:: ./img/224-0.png
       :align: center
    
    2. A simple map of the country is displayed:

    .. image:: ./img/225-0.png
       :align: center
    
    3. The user selects a desired radius in miles, and clicks on a point within the country:

    .. image:: ./img/225-1.png
       :align: center

    4. The system identifies all of the cities and towns within the given radius of that point:

    .. image:: ./img/226-0.png
       :align: center
       :scale: 40

    5. Finally, the resulting features are displayed at a higher resolution for the user to view or print:

    .. image:: ./img/227-0.png
       :align: center
       :scale: 40

    While we haven't yet looked at the map-rendering and user-interface aspects of geospatial applications, we do know enough to proceed with a very simple implementation of the DISTAL system. In this implementation, we will make use of basic CGI scripts and a "black box" map-generator module, while focusing on the data storage and manipulation aspects of the DISTAL application.

    Note that *Chapter 8, Using Python and Mapnik to Produce Maps*, will look at the details of generating maps using the Mapnik map-rendering toolkit, while *Chapter 9, Putting It All Together – a Complete Mapping System*, will look at the user-interface aspects of building a sophisticated web-based geospatial application. If you wanted to, you could rewrite the DISTAL implementation using the information in the next two chapters to produce a more robust and fully-functional version of the DISTAL application that could be deployed on the Internet.

.. tab:: 英文

    The DISTAL application will have the following basic workflow:

    1. The user starts by selecting the country they wish to work with:

    .. image:: ./img/224-0.png
       :align: center
    
    2. A simple map of the country is displayed:

    .. image:: ./img/225-0.png
       :align: center
    
    3. The user selects a desired radius in miles, and clicks on a point within the country:

    .. image:: ./img/225-1.png
       :align: center

    4. The system identifies all of the cities and towns within the given radius of that point:

    .. image:: ./img/226-0.png
       :align: center
       :scale: 40

    5. Finally, the resulting features are displayed at a higher resolution for the user to view or print:

    .. image:: ./img/227-0.png
       :align: center
       :scale: 40

    While we haven't yet looked at the map-rendering and user-interface aspects of geospatial applications, we do know enough to proceed with a very simple implementation of the DISTAL system. In this implementation, we will make use of basic CGI scripts and a "black box" map-generator module, while focusing on the data storage and manipulation aspects of the DISTAL application.

    Note that *Chapter 8, Using Python and Mapnik to Produce Maps*, will look at the details of generating maps using the Mapnik map-rendering toolkit, while *Chapter 9, Putting It All Together – a Complete Mapping System*, will look at the user-interface aspects of building a sophisticated web-based geospatial application. If you wanted to, you could rewrite the DISTAL implementation using the information in the next two chapters to produce a more robust and fully-functional version of the DISTAL application that could be deployed on the Internet.
