关于 DISTAL
============================================

About DISTAL

.. tab:: 中文

   DISTAL 应用程序的基本工作流程如下：

   1. 用户首先选择他们想要使用的国家：

   .. image:: ./img/224-0.png  
      :align: center

   2. 显示该国家的简单地图：

   .. image:: ./img/225-0.png  
      :align: center

   3. 用户选择所需的半径（以英里为单位），然后点击国家内的某个点：

   .. image:: ./img/225-1.png  
      :align: center

   4. 系统会识别出该点给定半径内的所有城市和城镇：

   .. image:: ./img/226-0.png  
      :align: center  
      :scale: 40

   5. 最后，结果特征以更高分辨率显示，供用户查看或打印：

   .. image:: ./img/227-0.png  
      :align: center  
      :scale: 40

   尽管我们尚未详细讨论地理空间应用程序的地图渲染和用户界面方面，但我们已经掌握了足够的知识，可以继续实现一个非常简单的 DISTAL 系统。在此实现中，我们将使用基本的 CGI 脚本和一个“黑盒”地图生成模块，专注于 DISTAL 应用程序的数据存储和操作方面。

   请注意，*第8章，使用 Python 和 Mapnik 生成地图* 将详细介绍如何使用 Mapnik 地图渲染工具包生成地图，而 *第9章，整合一切——一个完整的映射系统* 将讨论构建一个复杂的基于 Web 的地理空间应用程序的用户界面方面。如果您愿意，您可以使用接下来的两章中的信息重写 DISTAL 实现，从而生成一个更强大、功能完整的 DISTAL 应用程序版本，并可在互联网上部署。

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
