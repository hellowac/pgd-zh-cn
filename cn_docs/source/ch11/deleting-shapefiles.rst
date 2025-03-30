删除 shapefiles
============================================

Deleting shapefiles

.. tab:: 中文

    The final piece of functionality we'll need to implement is the Delete shapefile
    view. This will let the user delete an entire uploaded shapefile. The process is
    basically the same as for deleting features; we've already got a **Delete** hyperlink
    on the main page, so all we have to do is implement the underlying view.

    Go to the editor application's urls.py module and add the following entry to the URL pattern list::

        url(r'^delete/(?P<shapefile_id>\d+)$', 'delete_shapefile'),

    Then edit views.py and add the following new view function::

        def delete_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            if request.method == "GET":
                return render(request, "delete_shapefile.html",
                              {'shapefile' : shapefile})

            elif request.method == "POST":
                if request.POST['confirm'] == "1":
                    shapefile.delete()
                return HttpResponseRedirect("/editor")

    Notice that we're passing the Shapefile object to the template. This is because we
    want to display some information about the shapefile on the confirmation page.

    .. note::

        Remember that shapefile.delete()doesn't just delete the
        Shapefile object itself; it also deletes all the objects associated
        with the Shapefile through ForeignKey fields. This means
        that the one call to shapefile.delete() will also delete
        all the Attribute, Feature, and AttributeValue objects
        associated with that shapefile.

    Finally, create a new template named delete_shapefile.html, and enter the
    following text into this file::
    
    .. code-block:: html

        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
            <body>
                <h1>Delete Shapefile</h1>
                <form method="POST">
                    Are you sure you want to delete the
                    "{{ shapefile.filename }}" shapefile?
                    <p/>
                    <button type="submit" name="confirm"
                        value="1">Delete</button>
                        &nbsp;
                    <button type="submit" name="confirm"
                        value="0">Cancel</button>
                </form>
            </body>
        </html>

    You should now be able to click on the **Delete** hyperlink to delete a shapefile. Go
    ahead and try it; you can always re-import your shapefile if you need it.

.. tab:: 英文

    The final piece of functionality we'll need to implement is the Delete shapefile
    view. This will let the user delete an entire uploaded shapefile. The process is
    basically the same as for deleting features; we've already got a **Delete** hyperlink
    on the main page, so all we have to do is implement the underlying view.

    Go to the editor application's urls.py module and add the following entry to the URL pattern list::

        url(r'^delete/(?P<shapefile_id>\d+)$', 'delete_shapefile'),

    Then edit views.py and add the following new view function::

        def delete_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            if request.method == "GET":
                return render(request, "delete_shapefile.html",
                              {'shapefile' : shapefile})

            elif request.method == "POST":
                if request.POST['confirm'] == "1":
                    shapefile.delete()
                return HttpResponseRedirect("/editor")

    Notice that we're passing the Shapefile object to the template. This is because we
    want to display some information about the shapefile on the confirmation page.

    .. note::

        Remember that shapefile.delete()doesn't just delete the
        Shapefile object itself; it also deletes all the objects associated
        with the Shapefile through ForeignKey fields. This means
        that the one call to shapefile.delete() will also delete
        all the Attribute, Feature, and AttributeValue objects
        associated with that shapefile.

    Finally, create a new template named delete_shapefile.html, and enter the
    following text into this file::
    
    .. code-block:: html

        <html>
            <head>
                <title>ShapeEditor</title>
            </head>
            <body>
                <h1>Delete Shapefile</h1>
                <form method="POST">
                    Are you sure you want to delete the
                    "{{ shapefile.filename }}" shapefile?
                    <p/>
                    <button type="submit" name="confirm"
                        value="1">Delete</button>
                        &nbsp;
                    <button type="submit" name="confirm"
                        value="0">Cancel</button>
                </form>
            </body>
        </html>

    You should now be able to click on the **Delete** hyperlink to delete a shapefile. Go
    ahead and try it; you can always re-import your shapefile if you need it.
