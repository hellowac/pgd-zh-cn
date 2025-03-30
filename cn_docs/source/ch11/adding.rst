添加要素
============================================

Adding features

.. tab:: 中文

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
    
    Then edit the select_feature.html template and add the following highlighted lines to the body of this template::

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
    
    Then edit the select_feature.html template and add the following highlighted lines to the body of this template::

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
