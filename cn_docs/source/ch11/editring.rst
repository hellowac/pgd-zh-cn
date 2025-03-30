编辑要素
============================================

Editing features

.. tab:: 中文

    Now that we know which feature the user wants to edit, our next task is to
    implement the edit feature page itself. To do this, we are going to have to create a
    custom form with a single input field, named geometry, that uses a map-editing
    widget for editing the feature's geometry.

    To create this form, we're going to borrow elements from GeoDjango's built-in
    "admin" interface, in particular the django.contrib.gis.admin.GeoModelAdmin
    class. This class provides a method named get_map_widget() which returns an
    editing widget which we can then include in a custom-generated form.

    The process of building this form is a bit involved, thanks to the fact that we have
    to create a new django.forms.Form subclass on-the-fly to be handle the different
    types of geometries which can be edited. Let's put this complexity into a new
    function within the shared.utils module, which we'll call get_map_form().

    Edit the utils.py module and type in the following code::

        def get_map_form(shapefile):
            geometry_field = calc_geometry_field(shapefile.geom_type)
            admin_instance = admin.GeoModelAdmin(Feature, admin.site)
            field       = Feature._meta.get_field(geometry_field)
            widget_type = admin_instance.get_map_widget(field)

            class MapForm(forms.Form):
                geometry = forms.CharField(widget=widget_type(),
                                            label="")
            return MapForm

    You'll also need to add the following import statements to the top of the file::

        from django import forms
        from django.contrib.gis import admin
        from shapeEditor.shared.models import Feature

    The get_map_form() function makes use of a GeoModelAdmin instance. We met
    GeoModelAdmin earlier in this chapter when we explored GeoDjango's built-in
    admin interface; here we are using it to generate an appropriate map widget for
    editing the type of geometry stored in the current shapefile.

    Using the GeoModelAdmin instance, the get_map_form() function creates and
    returns a new django.forms.Form subclass with the appropriate widget type used
    to edit this particular shapefile's features. Note that the get_map_form() function
    returns the MapForm class, rather than an instance of that class; we'll use the returned
    class to create the appropriate MapForm instances as we need them.

    With this function behind us, we can now implement the rest of the edit feature
    view. Let's start by setting up the view's URL; open the editor application's urls.
    py module and add the following to the list of URL patterns::

        url(r'^edit_feature/(?P<shapefile_id>\d+)/' +
            r'(?P<feature_id>\d+)$', 'edit_feature'),

    We're now ready to implement the view function itself. Edit the views.py module
    and start defining the edit_feature() function::

        def edit_feature(request, shapefile_id, feature_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except ShapeFile.DoesNotExist:
                    return HttpResponseNotFound()

            try:
                feature = Feature.objects.get(id=feature_id)
            except Feature.DoesNotExist:
                return HttpResponseNotFound()

    So far this is quite straightforward: we load the Shapefile object for the current
    shapefile, and the Feature object for the feature we are editing. We next want to load
    into memory a list of that feature's attributes, so these can be displayed to the user::

        attributes = []
        for attr_value in feature.attributevalue_set.all():
            attributes.append([attr_value.attribute.name,
                               attr_value.value])
        attributes.sort()
        
    This is where things get interesting. We need to create a Django Form object
    (actually, an instance of the MapForm class created dynamically by the get_map_
    form() function we wrote earlier), and use this form instance to display the feature
    to be edited. When the form is submitted, we'll extract the updated geometry and
    save it back into the Feature object again, before redirecting the user back to the
    edit shapefile page to select another feature.

    As we saw when we created the import shapefile form, the basic Django idiom
    for processing a form is as follows::

        if request.method == "GET":
            form = MyForm()
            return render(request, "template.html",
                            {'form' : form})
        elif request.method == "POST":
            form = MyForm(request.POST)
            if form.is_valid():
                # Extract and save the form's contents here...
                return HttpResponseRedirect("/somewhere/else")
            return render(request, "template.html",
                          {'form' : form})
    
    When the form is to be displayed for the first time, request.method will be set to
    GET. In this case, we create a new form object and display the form as part of an
    HTML template. When the form is submitted by the user, request.method will be
    set to POST. In this case, a new form object is created that is bound to the submitted
    POST arguments. The form's contents are then checked, and if they are valid they are
    saved and the user is redirected back to some other page. If the form is not valid, it
    will be displayed again along with a suitable error message.

    Let's see how this idiom is used by the edit feature view. Add the following to
    the end of your new view function::

        geometry_field = \
            utils.calc_geometry_field(shapefile.geom_type)
        form_class     = utils.get_map_form(shapefile)

        if request.method == "GET":
            wkt = getattr(feature, geometry_field)
            form = form_class({'geometry' : wkt})
            return render(request, "edit_feature.html",
                          {'shapefile' : shapefile,
                          'form': form,
                          'attributes' : attributes})
        elif request.method == "POST":
            form = form_class(request.POST)
            try:
                if form.is_valid():
                    wkt = form.cleaned_data['geometry']
                    setattr(feature, geometry_field, wkt)
                    feature.save()

                    return HttpResponseRedirect("/editor/edit/" +
                        shapefile_id)
                except ValueError:
                    pass

                return render(request, "edit_feature.html",
                              {'shapefile' : shapefile,
                              'form': form,
                              'attributes' : attributes})

    As you can see, we call utils.get_map_form() to create a new django.forms.Form
    subclass which will be used to edit the feature's geometry. We also call utils.calc_
    geometry_field() to see which field in the Feature object should be edited.

    The rest of this function pretty much follows the Django idiom for form-processing.
    The only interesting thing to note is that we get and set the geometry field (using the
    getattr() and setattr() functions, respectively) in WKT format. GeoDjango treats
    geometry fields as if they were character fields which hold the geometry in WKT
    format. The GeoDjango JavaScript code then takes that WKT data (which is stored
    in a hidden form field named geometry) and passes it to OpenLayers for display as
    a vector geometry. OpenLayers allows the user to edit that vector geometry, and the
    updated geometry is stored back into the hidden geometry field as WKT data. We
    then extract that updated geometry's WKT text, and store it back into the Feature
    object again.

    So much for the edit_feature() view function. Let's now create the template
    used by this view. Create a new file named edit_feature.html within the editor
    application's templates directory, and enter the following text into this file:

    .. code-block:: html

        <html>
            <head>
                <title>ShapeEditor</title>
                <script src="http://openlayers.org/api/OpenLayers.js">
                </script>
            </head>
            <body>
                <h1>Edit Feature</h1>
                <form method="POST" action="">
                    <table>
                    {{ form.as_table }}
                    <tr>
                        <td></td>
                        <td align="right">
                            <table>
                            {% for attr in attributes %}
                                <tr>
                                    <td>{{ attr.0 }}</td>
                                    <td>{{ attr.1 }}</td>
                                </tr>
                            {% endfor %}
                            </table>
                        </td>
                    </tr>
                    <tr>
                        <td></td>
                        <td align="center">
                            <input type="submit" value="Save"/>
                            &nbsp;
                            <button type="button" onClick='window.location="/editor/
                                edit/{{ shapefile.id }}";'>
                                Cancel
                            </button>
                        </td>
                    </tr>
                    </table>
                </form>
            </body>
        </html>

    This template uses an HTML table to display the form, and uses the form.as_table
    template function call to render the form as HTML table rows. We then display the
    list of feature attributes within a sub-table, and finally include Save and Cancel
    buttons at the bottom.

    With all this code written, we are finally able to edit features within the ShapeEditor:

    .. image:: ./img/483-0.png
       :align: center

    Within this editor, you can make use of a number of GeoDjango's built-in features to
    edit the geometry:

    - You can click on the **Edit Geometry** tool (|img1| ) to select a feature for editing.
    - You can click on the **Add Geometry** tool (|img2| ) to start drawing a new geometry.
    - When a geometry is selected, you can click on a dark circle and drag it to move the endpoints of a line segment.
    - When a geometry is selected, you can click on a light circle to split an existing line segment in two, making a new point which can then be dragged.
    - If you hold the mouse down over a dark circle, you can press the Delete key (or type D) to delete that point. Note that this only works if the geometry has more than three points.
    - You can click on the **Delete all Features** hyperlink to delete the current feature's geometries. We'll look at this hyperlink in more detail shortly.
    
    Once you have finished editing the feature, you can click on the **Save** button to save
    the edited features, or the **Cancel** button to abandon the changes.

    While this is all working well, there is one rather annoying quirk: GeoDjango lets
    the user remove the geometries from a map by using a hyperlink named **Delete
    all Features**. Since we're currently editing a single feature, this hyperlink is rather
    confusingly named: what it actually does is delete the geometries for this feature, not the
    feature itself. Let's change the text of this hyperlink to something more meaningful.

    Go to the copy of Django that you downloaded, and navigate to the contrib/gis/templates/gis/admin directory. In this directory is a file named openlayers.
    html. Take a copy of this file, and move it into your editor application's templates
    directory, renaming it to openlayers-custom.html.
    
    Open your copy of this file, and look near the bottom for the text Delete all
    Features. Change this to Clear Feature's Geometry, and save your changes.
    
    So far so good. Now we need to tell the GeoDjango editing widget to use our custom
    version of the openlayers.html file. To do this, edit your utils.py module and
    find your definition of the get_map_form() function. Replace the line which defines
    the admin_instance variable with the following highlighted lines::
    
        def get_map_form(shapefile):
            geometry_field = calc_geometry_field(shapefile.geom_type)

            class CustomGeoModelAdmin(admin.GeoModelAdmin):
                map_template = "openlayers-custom.html"

            adminInstance = CustomGeoModelAdmin(Feature, admin.site)
            field = Feature._meta.get_field(geometry_field)
            widget_type = admin_instance.get_map_widget(field)
            
            class MapForm(forms.Form):
                geometry = forms.CharField(widget=widget_type(), label="")

            return MapForm

    If you then try editing a feature, you'll see that your customized version of the openlayers.html file is being used:

    .. image:: ./img/485-0.png
       :align: center
       :class: with-border
    
    By replacing the template, and by creating your own custom subclass of
    GeoModelAdmin, you can make various changes to the appearance and functionality
    of the built-in editing widget. If you want to see what is possible, take a look at the
    modules in the django.contrib.gis.admin directory.

.. tab:: 英文

    Now that we know which feature the user wants to edit, our next task is to
    implement the edit feature page itself. To do this, we are going to have to create a
    custom form with a single input field, named geometry, that uses a map-editing
    widget for editing the feature's geometry.

    To create this form, we're going to borrow elements from GeoDjango's built-in
    "admin" interface, in particular the django.contrib.gis.admin.GeoModelAdmin
    class. This class provides a method named get_map_widget() which returns an
    editing widget which we can then include in a custom-generated form.

    The process of building this form is a bit involved, thanks to the fact that we have
    to create a new django.forms.Form subclass on-the-fly to be handle the different
    types of geometries which can be edited. Let's put this complexity into a new
    function within the shared.utils module, which we'll call get_map_form().

    Edit the utils.py module and type in the following code::

        def get_map_form(shapefile):
            geometry_field = calc_geometry_field(shapefile.geom_type)
            admin_instance = admin.GeoModelAdmin(Feature, admin.site)
            field       = Feature._meta.get_field(geometry_field)
            widget_type = admin_instance.get_map_widget(field)

            class MapForm(forms.Form):
                geometry = forms.CharField(widget=widget_type(),
                                            label="")
            return MapForm

    You'll also need to add the following import statements to the top of the file::

        from django import forms
        from django.contrib.gis import admin
        from shapeEditor.shared.models import Feature

    The get_map_form() function makes use of a GeoModelAdmin instance. We met
    GeoModelAdmin earlier in this chapter when we explored GeoDjango's built-in
    admin interface; here we are using it to generate an appropriate map widget for
    editing the type of geometry stored in the current shapefile.

    Using the GeoModelAdmin instance, the get_map_form() function creates and
    returns a new django.forms.Form subclass with the appropriate widget type used
    to edit this particular shapefile's features. Note that the get_map_form() function
    returns the MapForm class, rather than an instance of that class; we'll use the returned
    class to create the appropriate MapForm instances as we need them.

    With this function behind us, we can now implement the rest of the edit feature
    view. Let's start by setting up the view's URL; open the editor application's urls.
    py module and add the following to the list of URL patterns::

        url(r'^edit_feature/(?P<shapefile_id>\d+)/' +
            r'(?P<feature_id>\d+)$', 'edit_feature'),

    We're now ready to implement the view function itself. Edit the views.py module
    and start defining the edit_feature() function::

        def edit_feature(request, shapefile_id, feature_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except ShapeFile.DoesNotExist:
                    return HttpResponseNotFound()

            try:
                feature = Feature.objects.get(id=feature_id)
            except Feature.DoesNotExist:
                return HttpResponseNotFound()

    So far this is quite straightforward: we load the Shapefile object for the current
    shapefile, and the Feature object for the feature we are editing. We next want to load
    into memory a list of that feature's attributes, so these can be displayed to the user::

        attributes = []
        for attr_value in feature.attributevalue_set.all():
            attributes.append([attr_value.attribute.name,
                               attr_value.value])
        attributes.sort()
        
    This is where things get interesting. We need to create a Django Form object
    (actually, an instance of the MapForm class created dynamically by the get_map_
    form() function we wrote earlier), and use this form instance to display the feature
    to be edited. When the form is submitted, we'll extract the updated geometry and
    save it back into the Feature object again, before redirecting the user back to the
    edit shapefile page to select another feature.

    As we saw when we created the import shapefile form, the basic Django idiom
    for processing a form is as follows::

        if request.method == "GET":
            form = MyForm()
            return render(request, "template.html",
                            {'form' : form})
        elif request.method == "POST":
            form = MyForm(request.POST)
            if form.is_valid():
                # Extract and save the form's contents here...
                return HttpResponseRedirect("/somewhere/else")
            return render(request, "template.html",
                          {'form' : form})
    
    When the form is to be displayed for the first time, request.method will be set to
    GET. In this case, we create a new form object and display the form as part of an
    HTML template. When the form is submitted by the user, request.method will be
    set to POST. In this case, a new form object is created that is bound to the submitted
    POST arguments. The form's contents are then checked, and if they are valid they are
    saved and the user is redirected back to some other page. If the form is not valid, it
    will be displayed again along with a suitable error message.

    Let's see how this idiom is used by the edit feature view. Add the following to
    the end of your new view function::

        geometry_field = \
            utils.calc_geometry_field(shapefile.geom_type)
        form_class     = utils.get_map_form(shapefile)

        if request.method == "GET":
            wkt = getattr(feature, geometry_field)
            form = form_class({'geometry' : wkt})
            return render(request, "edit_feature.html",
                          {'shapefile' : shapefile,
                          'form': form,
                          'attributes' : attributes})
        elif request.method == "POST":
            form = form_class(request.POST)
            try:
                if form.is_valid():
                    wkt = form.cleaned_data['geometry']
                    setattr(feature, geometry_field, wkt)
                    feature.save()

                    return HttpResponseRedirect("/editor/edit/" +
                        shapefile_id)
                except ValueError:
                    pass

                return render(request, "edit_feature.html",
                              {'shapefile' : shapefile,
                              'form': form,
                              'attributes' : attributes})

    As you can see, we call utils.get_map_form() to create a new django.forms.Form
    subclass which will be used to edit the feature's geometry. We also call utils.calc_
    geometry_field() to see which field in the Feature object should be edited.

    The rest of this function pretty much follows the Django idiom for form-processing.
    The only interesting thing to note is that we get and set the geometry field (using the
    getattr() and setattr() functions, respectively) in WKT format. GeoDjango treats
    geometry fields as if they were character fields which hold the geometry in WKT
    format. The GeoDjango JavaScript code then takes that WKT data (which is stored
    in a hidden form field named geometry) and passes it to OpenLayers for display as
    a vector geometry. OpenLayers allows the user to edit that vector geometry, and the
    updated geometry is stored back into the hidden geometry field as WKT data. We
    then extract that updated geometry's WKT text, and store it back into the Feature
    object again.

    So much for the edit_feature() view function. Let's now create the template
    used by this view. Create a new file named edit_feature.html within the editor
    application's templates directory, and enter the following text into this file:

    .. code-block:: html

        <html>
            <head>
                <title>ShapeEditor</title>
                <script src="http://openlayers.org/api/OpenLayers.js">
                </script>
            </head>
            <body>
                <h1>Edit Feature</h1>
                <form method="POST" action="">
                    <table>
                    {{ form.as_table }}
                    <tr>
                        <td></td>
                        <td align="right">
                            <table>
                            {% for attr in attributes %}
                                <tr>
                                    <td>{{ attr.0 }}</td>
                                    <td>{{ attr.1 }}</td>
                                </tr>
                            {% endfor %}
                            </table>
                        </td>
                    </tr>
                    <tr>
                        <td></td>
                        <td align="center">
                            <input type="submit" value="Save"/>
                            &nbsp;
                            <button type="button" onClick='window.location="/editor/
                                edit/{{ shapefile.id }}";'>
                                Cancel
                            </button>
                        </td>
                    </tr>
                    </table>
                </form>
            </body>
        </html>

    This template uses an HTML table to display the form, and uses the form.as_table
    template function call to render the form as HTML table rows. We then display the
    list of feature attributes within a sub-table, and finally include Save and Cancel
    buttons at the bottom.

    With all this code written, we are finally able to edit features within the ShapeEditor:

    .. image:: ./img/483-0.png
       :align: center

    Within this editor, you can make use of a number of GeoDjango's built-in features to
    edit the geometry:

    - You can click on the **Edit Geometry** tool (|img1| ) to select a feature for editing.
    - You can click on the **Add Geometry** tool (|img2| ) to start drawing a new geometry.
    - When a geometry is selected, you can click on a dark circle and drag it to move the endpoints of a line segment.
    - When a geometry is selected, you can click on a light circle to split an existing line segment in two, making a new point which can then be dragged.
    - If you hold the mouse down over a dark circle, you can press the Delete key (or type D) to delete that point. Note that this only works if the geometry has more than three points.
    - You can click on the **Delete all Features** hyperlink to delete the current feature's geometries. We'll look at this hyperlink in more detail shortly.
    
    Once you have finished editing the feature, you can click on the **Save** button to save
    the edited features, or the **Cancel** button to abandon the changes.

    While this is all working well, there is one rather annoying quirk: GeoDjango lets
    the user remove the geometries from a map by using a hyperlink named **Delete
    all Features**. Since we're currently editing a single feature, this hyperlink is rather
    confusingly named: what it actually does is delete the geometries for this feature, not the
    feature itself. Let's change the text of this hyperlink to something more meaningful.

    Go to the copy of Django that you downloaded, and navigate to the contrib/gis/templates/gis/admin directory. In this directory is a file named openlayers.
    html. Take a copy of this file, and move it into your editor application's templates
    directory, renaming it to openlayers-custom.html.
    
    Open your copy of this file, and look near the bottom for the text Delete all
    Features. Change this to Clear Feature's Geometry, and save your changes.
    
    So far so good. Now we need to tell the GeoDjango editing widget to use our custom
    version of the openlayers.html file. To do this, edit your utils.py module and
    find your definition of the get_map_form() function. Replace the line which defines
    the admin_instance variable with the following highlighted lines::
    
        def get_map_form(shapefile):
            geometry_field = calc_geometry_field(shapefile.geom_type)

            class CustomGeoModelAdmin(admin.GeoModelAdmin):
                map_template = "openlayers-custom.html"

            adminInstance = CustomGeoModelAdmin(Feature, admin.site)
            field = Feature._meta.get_field(geometry_field)
            widget_type = admin_instance.get_map_widget(field)
            
            class MapForm(forms.Form):
                geometry = forms.CharField(widget=widget_type(), label="")

            return MapForm

    If you then try editing a feature, you'll see that your customized version of the openlayers.html file is being used:

    .. image:: ./img/485-0.png
       :align: center
       :class: with-border
    
    By replacing the template, and by creating your own custom subclass of
    GeoModelAdmin, you can make various changes to the appearance and functionality
    of the built-in editing widget. If you want to see what is possible, take a look at the
    modules in the django.contrib.gis.admin directory.


.. |img1| image:: ./img/484-0.png

.. |img2| image:: ./img/484-1.png