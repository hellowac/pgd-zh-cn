最新动态
============================================

Recent developments

.. tab:: 中文

    十年前，地理空间开发远比今天受到更多限制。专业的（且昂贵）地理信息系统（GIS）是处理和可视化地理空间数据的常规工具。即使是开源工具，若有也很晦涩，且难以使用。更重要的是，一切都依赖于桌面计算机——通过互联网处理地理空间数据的概念无非是一个遥远的梦想。

    2005年，Google 发布了两款彻底改变地理空间开发面貌的产品。**Google Maps** 和 **Google Earth** 使得任何拥有网页浏览器或桌面计算机的人都能查看和处理地理空间数据。以前需要专家知识和多年的实践经验，现在即便是一个四岁的小孩也能立即查看和操作世界互动地图。

    Google 的产品并不完美：地图投影是故意简化的，这导致在显示叠加图层时出现错误和问题；这些产品仅限非商业用途免费；而且它们几乎没有进行地理空间分析的能力。尽管如此，它们对地理空间开发领域的影响巨大。人们意识到了可能性，地图和其底层的地理空间数据的使用变得如此普遍，以至于现在即便是手机也常常内置地图工具。

    **全球定位系统（GPS）** 对地理空间开发也产生了重大影响。街道以及其他人造和自然特征的地理空间数据曾经是一项昂贵且严格控制的资源，通常是通过扫描航拍照片，再手动绘制街道或海岸线轮廓来数字化所需特征。随着便宜且易于获取的便携式 GPS 单元的出现，任何想要的人现在都可以捕捉自己的地理空间数据。实际上，许多人将记录、编辑和提高街道及地形数据的准确性作为一种爱好，并通过互联网共享这些数据。所有这些意味着你不再仅限于记录自己的数据，或购买商业组织的数据；志愿提供的信息如今往往与商业数据一样准确和有用，甚至可能适用于你的地理空间应用。

    开源软件运动对地理空间开发也产生了重大影响。现在，通过自由提供的工具和库，你可以完全用开源工具构建复杂的地理空间应用程序，而不必依赖于商业工具集。由于这些工具的源代码通常是公开的，开发人员可以改进和扩展这些工具包，修复问题并增加新功能，从而造福所有人。像 PROJ.4、PostGIS、OGR 和 GDAL 这样的工具包，都是开源运动的受益者。在本书中，我们将使用这些工具。

    除了独立工具和库之外，许多地理空间的 **应用程序编程接口（APIs）** 已经问世。Google 提供了多种 API，可用于在网站中嵌入地图并执行有限的地理空间分析。其他服务，如我们之前使用的 OpenStreetMap 地理编码器，允许你执行一些地理空间任务，如果你仅限于使用自己的数据和编程资源，这些任务将变得十分困难。

    随着越来越多的地理空间数据从多个来源变得可用，以及能够处理这些数据的工具和系统数量的增加，定义地理空间数据标准变得越来越重要。 **开放地理空间联盟（OGC）** （http://www.opengeospatial.org）是一个国际标准组织，旨在提供一套标准格式和协议，用于共享和存储地理空间数据。这些标准，包括 GML、KML、GeoRSS、WMS、WFS 和 WCS，为地理空间数据提供了一种共享的“语言”。商业和开源的 GIS 系统、Google Earth、基于 Web 的 API 和像 OGR 这样的专门地理空间工具包，都能够与这些标准兼容。实际上，一个地理空间工具包的重要方面就是能够理解和转换这些不同格式之间的数据。

    随着 GPS 单元的普及，你现在可以在执行其他任务时记录你的位置数据。 **地理定位（Geolocation）**，即在执行某项任务时记录你的位置信息，变得越来越普遍。例如，Twitter 社交网络服务现在允许你在输入状态更新时记录并显示你当前的位置。当你接近办公室时，复杂的待办事项软件现在可以自动隐藏任何无法在当前位置完成的任务。你的手机还可以告诉你哪些朋友在附近，并且搜索结果可以过滤以仅显示附近的商家。所有这一切，实际上是当 GIS 系统曾经在主机计算机上运行，并由花费多年时间学习的专家操作时开始的趋势的延续。

    地理空间数据和应用程序多年来已“民主化”，使它们能在更多地方被更多人使用。从过去只有大型组织才能完成的工作，现在任何人都可以使用手持设备来完成。随着技术的不断进步，工具变得更强大，这一趋势肯定会持续下去。

.. tab:: 英文

    A decade ago, geospatial development was vastly more limited than it is today. Professional (and hugely expensive) Geographical Information Systems were the norm for working with and visualizing geospatial data. Open source tools, where they were available, were obscure and hard to use. What is more, everything ran on the desktop—the concept of working with geospatial data across the Internet was no more than a distant dream.

    In 2005, Google released two products that completely changed the face of geospatial development. **Google Maps** and **Google Earth** made it possible for anyone with a web browser or a desktop computer to view and work with geospatial data. Instead of requiring expert knowledge and years of practice, even a four-year old could instantly view and manipulate interactive maps of the world.

    Google's products are not perfect: the map projections are deliberately simplified, leading to errors and problems with displaying overlays; these products are only free for non-commercial use; and they include almost no ability to perform geospatial analysis. Despite these limitations, they have had a huge effect on the field of geospatial development. People became aware of what was possible, and the use of maps and their underlying geospatial data has become so prevalent that even cell phones now commonly include built-in mapping tools.

    The **Global Positioning System (GPS)** has also had a major influence on geospatial development. Geospatial data for streets and other man-made and natural features used to be an expensive and tightly controlled resource, often created by scanning aerial photographs and then manually drawing an outline of a street or coastline over the top to digitize the required features. With the advent of cheap and readily- available portable GPS units, anyone who wishes to can now capture their own geospatial data. Indeed, many people have made a hobby of recording, editing, and improving the accuracy of street and topological data, which are then freely shared across the Internet. All this means that you're not limited to recording your own data, or purchasing data from a commercial organization; volunteered information is now often as accurate and useful as commercially-available data, and may well be suitable for your geospatial application.

    The open source software movement has also had a major influence on geospatial development. Instead of relying on commercial toolsets, it is now possible to build complex geospatial applications entirely out of freely-available tools and libraries. Because the source code for these tools is often available, developers can improve and extend these toolkits, fixing problems and adding new features for the benefit of everyone. Tools such as PROJ.4, PostGIS, OGR, and GDAL are all excellent geospatial toolkits which are benefactors of the open source movement. We will be making use of all these tools throughout this book.

    As well as standalone tools and libraries, a number of geospatial **Application Programming Interfaces (APIs)** have become available. Google have provided a number of APIs, which can be used to include maps and perform limited geospatial analysis within a website. Other services, such as the OpenStreetMap geocoder we used earlier, allow you to perform various geospatial tasks that would be difficult to do if you were limited to using your own data and programming resources.

    As more and more geospatial data becomes available, from an increasing number of sources, and as the number of tools and systems which can work with this data also increases, it has become increasingly important to define standards for geospatial data. The **Open Geospatial Consortium**, often abbreviated to OGC (http://www. opengeospatial.org) is an international standards organization which aims to do precisely this: to provide a set of standard formats and protocols for sharing and storing geospatial data. These standards, including GML, KML, GeoRSS, WMS, WFS, and WCS, provide a shared "language" in which geospatial data can be expressed. Tools such as commercial and open source GIS systems, Google Earth, web-based APIs, and specialized geospatial toolkits such as OGR are all able to work with these standards. Indeed, an important aspect of a geospatial toolkit is the ability to understand and translate data between these various formats.

    As GPS units have become more ubiquitous, it has become possible to record your location data as you are performing another task. **Geolocation**, the act of recording your location as you are doing something, is becoming increasingly common. The Twitter social networking service, for example, now allows you to record and display your current location as you enter a status update. As you approach your office, sophisticated To-do list software can now automatically hide any tasks which can't be done at that location. Your phone can also tell you which of your friends are nearby, and search results can be filtered to only show nearby businesses. All of this is simply the continuation of a trend that started when GIS systems were housed on mainframe computers and operated by specialists who spent years learning about them.

    Geospatial data and applications have been "democratized" over the years, making them available in more places, to more people. What was possible only in a large organization can now be done by anyone using a handheld device. As technology continues to improve, and the tools become more powerful, this trend is sure to continue.