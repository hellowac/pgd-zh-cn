定义 ShapeEditor 的应用程序
============================================

Defining the ShapeEditor's applications

.. tab:: 中文

    我们现在有了一个用于整个 ShapeEditor 系统的 Django 项目。接下来，我们需要按照 Django 的设计理念，将我们的项目分解为几个相关的小型且相对独立的应用程序：

    - 导入和导出 shapefile
    - 选择要素
    - 编辑要素
    - 瓦片地图服务器

    让我们为我们的应用程序选择一些简短且切题的名称：

    - importer（导入器）
    - exporter（导出器）
    - selector（选择器）
    - editor（编辑器）
    - tms（瓦片地图服务）

    我们还将定义一个名为 `shared` 的应用程序，用于存放这些应用程序共享的数据库模型和 Python 模块。例如，我们可能有一个名为 `attributeReader.py` 的模块，它是导入器和编辑器应用程序所需要的。我们会将其放入 `shared` 应用程序中，以明确表示此代码旨在供系统其他部分使用。

.. tab:: 英文

    We now have a Django project for our overall ShapeEditor system. We next need to
    break down our project into several related applications, following Django's design
    philosophy of having applications be small and relatively self-contained. Looking
    back at our design for the overall project, we can see several possible candidates for
    breaking the functionality into separate applications:

    - Importing and exporting shapefiles
    - Selecting features
    - Editing features
    - The Tile Map Server
    
    Let's choose some names for our applications, keeping them short and to the point:
    
    - importer
    - exporter
    - selector
    - editor
    - tms
    
    We will define one more application, which we'll called shared, to hold the
    database models and Python modules that are shared across these applications.
    For example, we might have a module named attributeReader.py that is needed
    by the importer and editor applications. We'll place this into the shared application
    to make it clear that this code is designed to be used elsewhere in the system.
