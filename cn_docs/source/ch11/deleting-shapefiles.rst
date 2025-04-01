删除 shapefiles
============================================

Deleting shapefiles

.. tab:: 中文

    我们需要实现的最后一个功能是删除 shapefile 视图。这将允许用户删除整个上传的 shapefile。其过程与删除功能基本相同；我们在主页上已经有一个 **Delete** 超链接，所以我们只需要实现底层视图。

    转到编辑器应用程序的 urls.py 模块，并在 URL 模式列表中添加以下条目::

        url(r'^delete/(?P<shapefile_id>\d+)$', 'delete_shapefile'),

    然后编辑 views.py 并添加以下新视图函数::

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

    请注意，我们将 Shapefile 对象传递给模板。这是因为我们希望在确认页面上显示一些有关 shapefile 的信息。

    .. note::

        请记住，shapefile.delete() 不仅仅会删除 Shapefile 对象本身；它还会通过 ForeignKey 字段删除与 Shapefile 关联的所有对象。这意味着一次调用 shapefile.delete() 也会删除与该 shapefile 关联的所有 Attribute、Feature 和 AttributeValue 对象。

    最后，创建一个名为 delete_shapefile.html 的新模板，并在该文件中输入以下文本：

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

    您现在应该能够点击 **Delete** 超链接来删除 shapefile。继续尝试；如果需要，您可以随时重新导入 shapefile。

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
    following text into this file:
    
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
