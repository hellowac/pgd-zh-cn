删除要素
============================================

Deleting features

.. tab:: 中文

    接下来我们希望让用户删除现有功能。为此，我们将在编辑功能视图中添加一个删除功能按钮。点击此按钮将把用户重定向到该功能的删除功能视图。

    编辑 edit_feature.html 模板，并在模板的 <form> 部分中添加以下高亮显示的行：

    .. code-block:: html

        <form method="POST" action="">
            <table>
                <tr>
                    <td></td>
                    <td align="right">
                        <input type="submit" name="delete"
                            value="Delete Feature"/>
                    </td>
                </tr>
                {{ form.as_table }}
                ...

    请注意，我们为此按钮使用了 <input type="submit">。这将提交表单，并带有一个名为 delete 的额外 POST 参数。现在再次打开编辑器应用程序的 views.py 模块，并在 edit_feature() 函数的顶部添加以下内容::

        if request.method == "POST" and "delete" in request.POST:
            return HttpResponseRedirect("/editor/delete_feature/" +
                                        shapefile_id+"/"+feature_id)

    接下来我们需要创建删除功能视图。打开编辑器应用程序的 urls.py 并在 URL 模式列表中添加以下内容::

        url(r'^delete_feature/(?P<shapefile_id>\d+)/' +
            r'(?P<feature_id>\d+)$', 'delete_feature'),

    接下来，在模板目录中创建一个名为 delete_feature.html 的新文件，并在该文件中输入以下文本：

    .. code-block:: html

        <html>
        <head>
            <title>ShapeEditor</title>
        </head>
        <body>
            <h1>Delete Feature</h1>
            <form method="POST">
                Are you sure you want to delete this feature?
                <p/>
                <button type="submit" name="confirm"
                    value="1">Delete</button>
                    &nbsp;
                <button type="submit" name="confirm"
                value="0">Cancel</button>
            </form>
        </body>
        </html>

    这是一个确认删除的简单 HTML 表单。当表单提交时，如果用户希望删除功能，名为 confirm 的 POST 参数将设置为 1。现在让我们实现使用此模板的视图。打开编辑器应用程序的 views.py 并添加以下新视图函数：

    .. code-block:: python

        def delete_feature(request, shapefile_id, feature_id):
            try:
                feature = Feature.objects.get(id=feature_id)
            except Feature.DoesNotExist:
                return HttpResponseNotFound()
            if request.method == "POST":
                if request.POST['confirm'] == "1":
                    feature.delete()
                return HttpResponseRedirect("/editor/edit/" + shapefile_id)
            
            return render(request, "delete_feature.html")

    如您所见，删除功能非常简单。

.. tab:: 英文

    We next want to let the user delete an existing feature. To do this, we'll add a Delete
    Feature button to the edit feature view. Clicking on this button will redirect the
    user to the delete feature view for that feature.

    Edit the edit_feature.html template, and add the following highlighted lines to
    the <form> section of the template:

    .. code-block:: html

        <form method="POST" action="">
            <table>
                <tr>
                    <td></td>
                    <td align="right">
                        <input type="submit" name="delete"
                            value="Delete Feature"/>
                    </td>
                </tr>
                {{ form.as_table }}
                ...

    Notice that we've used <input type="submit"> for this button. This will submit
    the form, with an extra POST parameter named delete. Now go back to the editor
    application's views.py module again, and add the following to the top of the edit_feature() function::

        if request.method == "POST" and "delete" in request.POST:
            return HttpResponseRedirect("/editor/delete_feature/" +
                                        shapefile_id+"/"+feature_id)

    We next want to create the delete feature view. Open the editor application's
    urls.py and add the following to the list of URL patterns::

        url(r'^delete_feature/(?P<shapefile_id>\d+)/' +
            r'(?P<feature_id>\d+)$', 'delete_feature'),

    Next, create a new file named delete_feature.html in the templates directory, and
    enter the following text into this file:

    .. code-block:: html

        <html>
        <head>
            <title>ShapeEditor</title>
        </head>
        <body>
            <h1>Delete Feature</h1>
            <form method="POST">
                Are you sure you want to delete this feature?
                <p/>
                <button type="submit" name="confirm"
                    value="1">Delete</button>
                    &nbsp;
                <button type="submit" name="confirm"
                value="0">Cancel</button>
            </form>
        </body>
        </html>

    This is a simple HTML form that confirms the deletion. When the form is submitted,
    the POST parameter named confirm will be set to 1 if the user wishes to delete the
    feature. Let's now implement the view which uses this template. Open the editor
    application's views.py and add the following new view function:
    
    .. code-block:: python

        def delete_feature(request, shapefile_id, feature_id):
            try:
                feature = Feature.objects.get(id=feature_id)
            except Feature.DoesNotExist:
                return HttpResponseNotFound()
            if request.method == "POST":
                if request.POST['confirm'] == "1":
                    feature.delete()
                return HttpResponseRedirect("/editor/edit/" + shapefile_id)
            
            return render(request, "delete_feature.html")

    As you can see, deleting features is quite straightforward.
