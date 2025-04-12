添加要素
============================================

Adding features

.. tab:: 中文

    接下来我们将实现添加新功能的能力。为此，我们将在编辑 shapefile 视图中添加一个 **Add Feature** 按钮。点击此按钮将调用“edit feature”URL，但没有 feature ID。然后我们将修改编辑功能视图，以便在没有给出 feature ID 的情况下创建一个新的 Feature 对象。

    打开编辑器应用程序的 views.py 模块，找到 edit_shapefile() 函数，并添加以下高亮显示的行到此函数中::

        def editshapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                raise Http404

            tms_url = "http://" + request.get_host() + "/tms/"
            find_feature_url = "http://" + request.get_host() \
                            + "/editor/find_feature"
            
            add_feature_url = "http://" + request.get_host() \
                            + "/editor/edit_feature/" \
                            + str(shapefile_id)
            return render(request, "select_feature.html",
                        {'shapefile': shapefile,
                        'find_feature_url' : find_feature_url,
                        'add_feature_url' : add_feature_url,
                        'tms_url': tms_url})

    然后编辑 select_feature.html 模板，并在此模板的主体中添加以下高亮显示的行：

    .. code-block:: html

        <body onload="init()">
            <h1>Edit Shapefile</h1>
            <b>Please choose a feature to edit</b>
            <br/>
            <div id="map" class="map"></div>
            <br/>
            <div style="margin-left:20px">
                <button type="button"
                    onClick='window.location="{{ add_feature_url }}";'>
                    Add Feature
                </button>
                <button type="button"
                    onClick='window.location="/shape-editor";'>
                    Cancel
                </button>
            </div>
        </body>

    这将在“选择功能”页面上放置一个 **Add Feature** 按钮。点击该按钮将调用 URL http://127.0.0.1:8000/editor/edit_feature/N（其中 N 是当前 shapefile 的记录 ID）。

    接下来我们需要添加一个 URL 模式来支持此 URL。打开编辑器应用程序的 urls.py 模块，并在 URL 模式列表中添加以下条目::

        url(r'^edit_feature/(?P<shapefile_id>\d+)$',
        'edit_feature'), # feature_id = None -> add.

    然后返回 views.py 并编辑 edit_feature() 函数的定义。将函数定义更改为如下所示::

        def editFeature(request, shapefile_id, feature_id=None):

    注意，feature_id 参数现在是可选的。现在找到以下代码块::

        try:
            feature = Feature.objects.get(id=feature_id)
        except Feature.DoesNotExist:
            return HttpResponseNotFound()

    您需要将此代码块替换为以下代码::

        if feature_id == None:
            feature = Feature(shapefile=shapefile)
        else:
            try:
                feature = Feature.objects.get(id=feature_id)
            except Feature.DoesNotExist:
                return HttpResponseNotFound()

    如果没有指定 feature_id，这将创建一个新的 Feature 对象，但如果指定了无效的 feature ID，则仍会失败。

    通过这些更改，您应该能够向 shapefile 添加新功能。继续尝试一下：如果 Django Web 服务器尚未运行，请运行它，然后单击导入的 shapefile 的编辑超链接。然后单击添加新功能超链接，并尝试创建一个新功能。新功能应显示在选择功能视图中：

    .. image:: ./img/487-0.png
        :align: center

.. tab:: 英文

    We'll next implement the ability to add a new feature. To do this, we'll put an **Add Feature** button onto the edit shapefile view. Clicking on this button will call the "edit feature" URL, but without a feature ID. We'll then modify the edit feature view so that if no feature ID is given a new Feature object is created.

    Open the editor application's views.py module, find the edit_shapefile()
    function, and add the following highlighted lines to this function::

        def editshapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                raise Http404

            tms_url = "http://"+request.get_host()+"/tms/"
            find_feature_url = "http://" + request.get_host() \
                            + "/editor/find_feature"
            
            add_feature_url = "http://" + request.get_host() \
                            + "/editor/edit_feature/" \
                            + str(shapefile_id)
            return render(request, "select_feature.html",
                        {'shapefile': shapefile,
                        'find_feature_url' : find_feature_url,
                        'add_feature_url' : add_feature_url,
                        'tms_url': tms_url})
    
    Then edit the select_feature.html template and add the following highlighted lines to the body of this template:

    .. code-block:: html

        <body onload="init()">
            <h1>Edit Shapefile</h1>
            <b>Please choose a feature to edit</b>
            <br/>
            <div id="map" class="map"></div>
            <br/>
            <div style="margin-left:20px">
                <button type="button"
                    onClick='window.location="{{ add_feature_url }}";'>
                    Add Feature
                </button>
                <button type="button"
                    onClick='window.location="/shape-editor";'>
                    Cancel
                </button>
            </div>
        </body>

    This will place an **Add Feature** button onto the "select feature" page. Clicking on that
    button will call the URL http://127.0.0.1:8000/editor/edit_feature/N (where
    N is the record ID of the current shapefile).

    We next need to add a URL pattern to support this URL. Open the editor
    application's urls.py module and add the following entry to the URL pattern list::

        url(r'^edit_feature/(?P<shapefile_id>\d+)$',
        'edit_feature'), # feature_id = None -> add.

    Then go back to views.py and edit the function definition for the edit_feature()
    function. Change the function definition to look as follows::

        def editFeature(request, shapefile_id, feature_id=None):

    Notice that the feature_id parameter is now optional. Now find the following
    block of code::

        try:
            feature = Feature.objects.get(id=feature_id)
        except Feature.DoesNotExist:
            return HttpResponseNotFound()

    You need to replace this block of code with the following::

        if feature_id == None:
            feature = Feature(shapefile=shapefile)
        else:
            try:
                feature = Feature.objects.get(id=feature_id)
            except Feature.DoesNotExist:
                return HttpResponseNotFound()

    This will create a new Feature object if the feature_id is not specified, but still
    fail if an invalid feature ID was specified.

    With these changes, you should be able to add a new feature to the shapefile.
    Go ahead and try it out: run the Django web server if it's not already running
    and click on the Edit hyperlink for your imported shapefile. Then click on the
    Add New Feature hyperlink, and try creating a new feature. The new feature
    should appear on the Select Feature view:

    .. image:: ./img/487-0.png
       :align: center
