导出 shapefiles
============================================

Exporting shapefiles

.. tab:: 中文

    Next, we need to implement the ability to export a shapefile. The process of
    exporting a shapefile is basically the reverse of the "import" logic, and involves
    the following steps:

    1. Create an OGR shapefile to receive the exported data.
    2. Save the features into the shapefile.
    3. Save the attributes into the shapefile.
    4. Compress the shapefile into a ZIP archive.
    5. Delete our temporary files.
    6. Send the ZIP file back to the user's web browser.
    
    All this work will take place in the shapefileIO application, with help from some
    utils.py functions. Before we begin, let's create an exporter module to handle the
    exporting process. Go to the shapefileIO directory, and create a new module named
    exporter.py. Initially, we're just going to add a dummy function to this module::
    
        def export_data(shapefile):
            return "More to come..."

    This function will take a desired Shapefile object, and return an HttpResponse
    that can be returned by the view function. This HttpResponse object will send the
    contents of the exported shapefile back to the user's web browser, where it can be
    saved to disk.

    Now let's create the view function that will call the exporter and return the HTTP
    response back to the caller. Go to the editor application's views.py module, and
    add the following new function::

        def export_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            return exporter.export_data(shapefile)

    This view function takes the record ID of the desired shapefile, loads the Shapefile
    object into memory, and passes it to the export_data() function for processing. The
    resulting HttpResponse object is then returned to the caller, allowing the exported
    file to be downloaded to the user's computer.

    While we are editing this file, add the following additional import statements
    to the top::

        from django.http import HttpResponseNotFound
        from shapeEditor.shapefileIO import exporter

    Note that the export_shapefile() view function takes an additional parameter,
    named shapefile_id. This parameter will be taken from the URL used to access
    the view, so that for example if the user accesses the URL http://127.0.0.1:8000/
    editor/export/1, the shapefile_id parameter will be set to the value 1.

    This is done by adding a special type of entry to the editor applications urls.py
    module. Edit this file, and add the following entry to the urlpatterns list::

        url(r'^export/(?P<shapefile_id>\d+)$', 'export_shapefile'),
    
    Our list_shapefiles.html template already makes use of this URL, adding the
    shapefile's record ID to the URL when the user clicks on the Export hyperlink::

        <a href="/editor/export/{{ shapefile.id }}">
            Export
        </a>

    Now that we've written our view function, we can start to implement the
    behind-the-scenes logic required to export and download the shapefile.
    All of this will be implemented in the exporter.py module.

.. tab:: 英文

    Next, we need to implement the ability to export a shapefile. The process of
    exporting a shapefile is basically the reverse of the "import" logic, and involves
    the following steps:

    1. Create an OGR shapefile to receive the exported data.
    2. Save the features into the shapefile.
    3. Save the attributes into the shapefile.
    4. Compress the shapefile into a ZIP archive.
    5. Delete our temporary files.
    6. Send the ZIP file back to the user's web browser.
    
    All this work will take place in the shapefileIO application, with help from some
    utils.py functions. Before we begin, let's create an exporter module to handle the
    exporting process. Go to the shapefileIO directory, and create a new module named
    exporter.py. Initially, we're just going to add a dummy function to this module::
    
        def export_data(shapefile):
            return "More to come..."

    This function will take a desired Shapefile object, and return an HttpResponse
    that can be returned by the view function. This HttpResponse object will send the
    contents of the exported shapefile back to the user's web browser, where it can be
    saved to disk.

    Now let's create the view function that will call the exporter and return the HTTP
    response back to the caller. Go to the editor application's views.py module, and
    add the following new function::

        def export_shapefile(request, shapefile_id):
            try:
                shapefile = Shapefile.objects.get(id=shapefile_id)
            except Shapefile.DoesNotExist:
                return HttpResponseNotFound()

            return exporter.export_data(shapefile)

    This view function takes the record ID of the desired shapefile, loads the Shapefile
    object into memory, and passes it to the export_data() function for processing. The
    resulting HttpResponse object is then returned to the caller, allowing the exported
    file to be downloaded to the user's computer.

    While we are editing this file, add the following additional import statements
    to the top::

        from django.http import HttpResponseNotFound
        from shapeEditor.shapefileIO import exporter

    Note that the export_shapefile() view function takes an additional parameter,
    named shapefile_id. This parameter will be taken from the URL used to access
    the view, so that for example if the user accesses the URL http://127.0.0.1:8000/
    editor/export/1, the shapefile_id parameter will be set to the value 1.

    This is done by adding a special type of entry to the editor applications urls.py
    module. Edit this file, and add the following entry to the urlpatterns list::

        url(r'^export/(?P<shapefile_id>\d+)$', 'export_shapefile'),
    
    Our list_shapefiles.html template already makes use of this URL, adding the
    shapefile's record ID to the URL when the user clicks on the Export hyperlink::

        <a href="/editor/export/{{ shapefile.id }}">
            Export
        </a>
        
    Now that we've written our view function, we can start to implement the
    behind-the-scenes logic required to export and download the shapefile.
    All of this will be implemented in the exporter.py module.

定义 OGR Shapefile
--------------------------------------------
Defining the OGR shapefile

.. tab:: 中文

    We'll use OGR to create the new shapefile that will hold the exported features.
    Let's start by creating a temporary directory to hold the shapefile's contents;
    replace your placeholder version of export_data() with the following::

        def exportData(shapefile):
            dst_dir = tempfile.mkdtemp()
            dst_file = str(os.path.join(dst_dir, shapefile.filename))

    Now that we've got somewhere to store the shapefile (and a filename for it),
    we'll create a spatial reference for the shapefile to use, and set up the shapefile's
    datasource and layer::

        dst_spatial_ref = osr.SpatialReference()
        dst_spatial_ref.ImportFromWkt(shapefile.srs_wkt)

        driver = ogr.GetDriverByName("ESRI Shapefile")
        datasource = driver.CreateDataSource(dst_file)
        layer = datasource.CreateLayer(str(shapefile.filename),
                                           dst_spatial_ref)

    .. note::

        Note that we're using str() to convert the shapefile's filename to an ASCII string. This is because Django uses Unicode strings, but OGR can't handle unicode filenames. We'll need to do the same thing for the attribute names.

    Now that we've created the shapefile itself, we next need to define the various fields which will hold the shapefile's attributes::

        for attr in shapefile.attribute_set.all():
            field = ogr.FieldDefn(str(attr.name), attr.type)
            field.SetWidth(attr.width)
            field.SetPrecision(attr.precision)
            layer.CreateField(field)

    Note how the information needed to define the field is taken directly from the
    Attribute object; Django makes iterating over the shapefile's attributes easy.

    That completes the definition of the shapefile. We're now ready to start saving
    data into the newly-created shapefile.

.. tab:: 英文

    We'll use OGR to create the new shapefile that will hold the exported features.
    Let's start by creating a temporary directory to hold the shapefile's contents;
    replace your placeholder version of export_data() with the following::

        def exportData(shapefile):
            dst_dir = tempfile.mkdtemp()
            dst_file = str(os.path.join(dst_dir, shapefile.filename))

    Now that we've got somewhere to store the shapefile (and a filename for it),
    we'll create a spatial reference for the shapefile to use, and set up the shapefile's
    datasource and layer::

        dst_spatial_ref = osr.SpatialReference()
        dst_spatial_ref.ImportFromWkt(shapefile.srs_wkt)

        driver = ogr.GetDriverByName("ESRI Shapefile")
        datasource = driver.CreateDataSource(dst_file)
        layer = datasource.CreateLayer(str(shapefile.filename),
                                           dst_spatial_ref)

    .. note::

        Note that we're using str() to convert the shapefile's filename to an ASCII string. This is because Django uses Unicode strings, but OGR can't handle unicode filenames. We'll need to do the same thing for the attribute names.

    Now that we've created the shapefile itself, we next need to define the various fields which will hold the shapefile's attributes::

        for attr in shapefile.attribute_set.all():
            field = ogr.FieldDefn(str(attr.name), attr.type)
            field.SetWidth(attr.width)
            field.SetPrecision(attr.precision)
            layer.CreateField(field)

    Note how the information needed to define the field is taken directly from the
    Attribute object; Django makes iterating over the shapefile's attributes easy.
    
    That completes the definition of the shapefile. We're now ready to start saving
    data into the newly-created shapefile.


将特征保存到 Shapefile
--------------------------------------------
Saving the features into the shapefile

.. tab:: 中文

    Because the shapefile can use any valid spatial reference, we'll need to transform
    the shapefile's features from the spatial reference used internally (EPSG 4326) into
    the shapefile's own spatial reference. Before we can do this, we'll need to set up an
    osr.CoordinateTransformation object to do the transformation::

        src_spatial_ref = osr.SpatialReference()
        src_spatial_ref.ImportFromEPSG(4326)

        coord_transform = osr.CoordinateTransformation(
                            src_spatial_ref, dst_spatial_ref)

    We'll also need to know which geometry field in the Feature object holds the
    feature's geometry data::

        geom_field = \
            utils.calc_geometry_field(shapefile.geom_type)

    With this information, we're ready to start exporting the shapefile's features::

        for feature in shapefile.feature_set.all():
            geometry = getattr(feature, geom_field)

    Right away, however, we encounter a problem. If you remember when we
    imported the shapefile, we had to "wrap" a Polygon or a LineString geometry into
    a MultiPolygon or MultiLineString so that the geometry types would be consistent
    in the database. Now that we're exporting the shapefile, we need to unwrap
    the geometry so that features that have only one Polygon or LineString in their
    geometries are saved as Polygons and LineStrings rather than MultiPolygons and
    MultiLineStrings. We'll use a utils.py function to do this unwrapping::

        geometry = utils.unwrap_geos_geometry(geometry)
    
    We'll implement this utils.py function shortly.

    Now that we've unwrapped the feature's geometry, we can go ahead and convert
    it back into an OGR geometry again, transform it into the shapefile's own spatial
    reference system, and create an OGR feature using that geometry::

        dst_geometry = ogr.CreateGeometryFromWkt(geometry.wkt)
        dst_geometry.Transform(coord_transform)
        
        dst_feature = ogr.Feature(layer.GetLayerDefn())
        dst_feature.SetGeometry(dst_geometry)

    Finally, we need to add the feature to the layer and call the Destroy() method to
    save the feature (and then the layer) into the shapefile::

        layer.CreateFeature(dst_feature)
        dst_feature.Destroy()

        datasource.Destroy()

    Before we move on, let's add our new unwrap_geos_geometry() function to utils.
    py. This code is quite straightforward, pulling a single Polygon or LineString object
    out of a MultiPolygon or MultiLineString if they contain only one geometry::

        def unwrap_geos_geometry(geometry):
            if geometry.geom_type in ["MultiPolygon",
                                      "MultiLineString"]:
            if len(geometry) == 1:
                geometry = geometry[0]

            return geometry

    So far so good; we've created the OGR feature, unwrapped the feature's geometry,
    and stored everything into the shapefile. Now we're ready to save the feature's
    attribute values.

.. tab:: 英文

    Because the shapefile can use any valid spatial reference, we'll need to transform
    the shapefile's features from the spatial reference used internally (EPSG 4326) into
    the shapefile's own spatial reference. Before we can do this, we'll need to set up an
    osr.CoordinateTransformation object to do the transformation::

        src_spatial_ref = osr.SpatialReference()
        src_spatial_ref.ImportFromEPSG(4326)

        coord_transform = osr.CoordinateTransformation(
                            src_spatial_ref, dst_spatial_ref)

    We'll also need to know which geometry field in the Feature object holds the
    feature's geometry data::

        geom_field = \
            utils.calc_geometry_field(shapefile.geom_type)

    With this information, we're ready to start exporting the shapefile's features::

        for feature in shapefile.feature_set.all():
            geometry = getattr(feature, geom_field)

    Right away, however, we encounter a problem. If you remember when we
    imported the shapefile, we had to "wrap" a Polygon or a LineString geometry into
    a MultiPolygon or MultiLineString so that the geometry types would be consistent
    in the database. Now that we're exporting the shapefile, we need to unwrap
    the geometry so that features that have only one Polygon or LineString in their
    geometries are saved as Polygons and LineStrings rather than MultiPolygons and
    MultiLineStrings. We'll use a utils.py function to do this unwrapping::

        geometry = utils.unwrap_geos_geometry(geometry)
    
    We'll implement this utils.py function shortly.

    Now that we've unwrapped the feature's geometry, we can go ahead and convert
    it back into an OGR geometry again, transform it into the shapefile's own spatial
    reference system, and create an OGR feature using that geometry::

        dst_geometry = ogr.CreateGeometryFromWkt(geometry.wkt)
        dst_geometry.Transform(coord_transform)
        
        dst_feature = ogr.Feature(layer.GetLayerDefn())
        dst_feature.SetGeometry(dst_geometry)

    Finally, we need to add the feature to the layer and call the Destroy() method to
    save the feature (and then the layer) into the shapefile::

        layer.CreateFeature(dst_feature)
        dst_feature.Destroy()

        datasource.Destroy()

    Before we move on, let's add our new unwrap_geos_geometry() function to utils.
    py. This code is quite straightforward, pulling a single Polygon or LineString object
    out of a MultiPolygon or MultiLineString if they contain only one geometry::

        def unwrap_geos_geometry(geometry):
            if geometry.geom_type in ["MultiPolygon",
                                      "MultiLineString"]:
            if len(geometry) == 1:
                geometry = geometry[0]

            return geometry
            
    So far so good; we've created the OGR feature, unwrapped the feature's geometry,
    and stored everything into the shapefile. Now we're ready to save the feature's
    attribute values.


将属性保存到 Shapefile
--------------------------------------------
Saving the attributes into the shapefile

.. tab:: 中文

    Our next task is to save the attribute values associated with each feature. When we
    imported the shapefile, we extracted the attribute values from the various OGR data
    types and converted them into strings so they could be stored into the database. This
    was done using the utils.get_ogr_feature_attribute() function. We now have
    to do the opposite: storing the string value into the OGR attribute field. As before,
    we'll use a utils.py function to do the hard work; add the following highlighted
    lines to the bottom of your export_data() function::

        ...
        dst_feature = ogr.Feature(layer.GetLayerDefn())
        dst_feature.SetGeometry(dst_geometry)

        for attr_value in feature.attributevalue_set.all():
            utils.set_ogr_feature_attribute(
                                    attr_value.attribute,
                                    attr_value.value,
                                    dst_feature,
                                    shapefile.encoding)
        
        layer.CreateFeature(dst_feature)
        dst_feature.Destroy()

        datasource.Destroy()

    .. note::

        You may be wondering what feature.attributevalue_set.
        all() does. Because the AttributeValue object includes a foreign
        key linking each attribute value to the associated Feature object, the
        Feature object can refer to the set of attribute values that link back
        to it, using attributevalue_set. Using this technique, we can
        scan through the list of attribute values for a feature using feature.
        attributevalue_set.all().

        If you want to learn more about these "reverse" foreign key lookups,
        see https://docs.djangoproject.com/en/dev/topics/db/queries/#related-objects.

    Now let's implement the set_ogr_feature_attribute() function within utils.
    py. As with the get_ogr_feature_attribute() function, set_ogr_feature_
    attribute() is rather tedious but straightforward: we have to deal with each OGR
    data type in turn, processing the string representation of the attribute value and calling
    the appropriate SetField() method to set the field's value. Here is the relevant code::

        def set_ogr_feature_attribute(attr, value, feature, encoding):
            attr_name = str(attr.name)
            if value == None:
                feature.UnsetField(attr_name)
                return

            if attr.type == ogr.OFTInteger:
                feature.SetField(attr_name, int(value))
            elif attr.type == ogr.OFTIntegerList:
                integers = eval(value)
                feature.SetFieldIntegerList(attr_name, integers)
            elif attr.type == ogr.OFTReal:
                feature.SetField(attr_name, float(value))
            elif attr.type == ogr.OFTRealList:
                floats = []
                for s in eval(value):
                    floats.append(eval(s))
                feature.SetFieldDoubleList(attr_name, floats)
            elif attr.type == ogr.OFTString:
                feature.SetField(attr_name, value.encode(encoding))
            elif attr.type == ogr.OFTStringList:
                strings = []
                for s in eval(value):
                    strings.append(s.encode(encoding))
                feature.SetFieldStringList(attr_name, strings)
            elif attr.type == ogr.OFTDate:
                parts = value.split(",")
                year  = int(parts[0])
                month = int(parts[1])
                day   = int(parts[2])
                tzone = int(parts[3])
                feature.SetField(attr_name, year, month, day,
                                 0, 0, 0, tzone)
            elif attr.type == ogr.OFTTime:
            parts  = value.split(",")
            hour   = int(parts[0])
            minute = int(parts[1])
            second = int(parts[2])
            tzone  = int(parts[3])
            feature.SetField(attr_name, 0, 0, 0,
                             hour, minute, second, tzone)
            elif attr.type == ogr.OFTDateTime:
                parts  = value.split(",")
                year   = int(parts[0])
                month  = int(parts[1])
                day    = int(parts[2])
                hour   = int(parts[3])
                minute = int(parts[4])
                second = int(parts[5])
                tzone = int(parts[6])
            feature.SetField(attr_name, year, month, day,
                             hour, minute, second, tzone)

.. tab:: 英文

    Our next task is to save the attribute values associated with each feature. When we
    imported the shapefile, we extracted the attribute values from the various OGR data
    types and converted them into strings so they could be stored into the database. This
    was done using the utils.get_ogr_feature_attribute() function. We now have
    to do the opposite: storing the string value into the OGR attribute field. As before,
    we'll use a utils.py function to do the hard work; add the following highlighted
    lines to the bottom of your export_data() function::

        ...
        dst_feature = ogr.Feature(layer.GetLayerDefn())
        dst_feature.SetGeometry(dst_geometry)

        for attr_value in feature.attributevalue_set.all():
            utils.set_ogr_feature_attribute(
                                    attr_value.attribute,
                                    attr_value.value,
                                    dst_feature,
                                    shapefile.encoding)
        
        layer.CreateFeature(dst_feature)
        dst_feature.Destroy()

        datasource.Destroy()

    .. note::

        You may be wondering what feature.attributevalue_set.
        all() does. Because the AttributeValue object includes a foreign
        key linking each attribute value to the associated Feature object, the
        Feature object can refer to the set of attribute values that link back
        to it, using attributevalue_set. Using this technique, we can
        scan through the list of attribute values for a feature using feature.
        attributevalue_set.all().

        If you want to learn more about these "reverse" foreign key lookups,
        see https://docs.djangoproject.com/en/dev/topics/db/queries/#related-objects.

    Now let's implement the set_ogr_feature_attribute() function within utils.
    py. As with the get_ogr_feature_attribute() function, set_ogr_feature_
    attribute() is rather tedious but straightforward: we have to deal with each OGR
    data type in turn, processing the string representation of the attribute value and calling
    the appropriate SetField() method to set the field's value. Here is the relevant code::

        def set_ogr_feature_attribute(attr, value, feature, encoding):
            attr_name = str(attr.name)
            if value == None:
                feature.UnsetField(attr_name)
                return

            if attr.type == ogr.OFTInteger:
                feature.SetField(attr_name, int(value))
            elif attr.type == ogr.OFTIntegerList:
                integers = eval(value)
                feature.SetFieldIntegerList(attr_name, integers)
            elif attr.type == ogr.OFTReal:
                feature.SetField(attr_name, float(value))
            elif attr.type == ogr.OFTRealList:
                floats = []
                for s in eval(value):
                    floats.append(eval(s))
                feature.SetFieldDoubleList(attr_name, floats)
            elif attr.type == ogr.OFTString:
                feature.SetField(attr_name, value.encode(encoding))
            elif attr.type == ogr.OFTStringList:
                strings = []
                for s in eval(value):
                    strings.append(s.encode(encoding))
                feature.SetFieldStringList(attr_name, strings)
            elif attr.type == ogr.OFTDate:
                parts = value.split(",")
                year  = int(parts[0])
                month = int(parts[1])
                day   = int(parts[2])
                tzone = int(parts[3])
                feature.SetField(attr_name, year, month, day,
                                 0, 0, 0, tzone)
            elif attr.type == ogr.OFTTime:
            parts  = value.split(",")
            hour   = int(parts[0])
            minute = int(parts[1])
            second = int(parts[2])
            tzone  = int(parts[3])
            feature.SetField(attr_name, 0, 0, 0,
                             hour, minute, second, tzone)
            elif attr.type == ogr.OFTDateTime:
                parts  = value.split(",")
                year   = int(parts[0])
                month  = int(parts[1])
                day    = int(parts[2])
                hour   = int(parts[3])
                minute = int(parts[4])
                second = int(parts[5])
                tzone = int(parts[6])
            feature.SetField(attr_name, year, month, day,
                             hour, minute, second, tzone)

压缩 Shapefile
--------------------------------------------
Compressing the shapefile

.. tab:: 中文

    Now that we've exported the desired data into an OGR shapefile, we can compress
    it into a ZIP archive. Go back to the exporter.py module and add the following to
    the end of your export_data() function::

        temp = tempfile.TemporaryFile()
        zip = zipfile.ZipFile(temp, 'w', zipfile.ZIP_DEFLATED)
        
        shapefile_base = os.path.splitext(dstFile)[0]
        shapefile_name = os.path.splitext(shapefile.filename)[0]
        
        for fName in os.listdir(dst_dir):
            zip.write(os.path.join(dst_dir, fName), fName)
        
        zip.close()

    Note that we use a temporary file, named temp, to store the ZIP archive's
    contents. We'll be returning temp to the user's web browser once the export
    process has finished.

.. tab:: 英文

    Now that we've exported the desired data into an OGR shapefile, we can compress
    it into a ZIP archive. Go back to the exporter.py module and add the following to
    the end of your export_data() function::

        temp = tempfile.TemporaryFile()
        zip = zipfile.ZipFile(temp, 'w', zipfile.ZIP_DEFLATED)
        
        shapefile_base = os.path.splitext(dstFile)[0]
        shapefile_name = os.path.splitext(shapefile.filename)[0]
        
        for fName in os.listdir(dst_dir):
            zip.write(os.path.join(dst_dir, fName), fName)
        
        zip.close()

    Note that we use a temporary file, named temp, to store the ZIP archive's
    contents. We'll be returning temp to the user's web browser once the export
    process has finished.


删除临时文件
--------------------------------------------
Deleting temporary files

.. tab:: 中文

    We next have to clean up after ourselves by deleting the shapefile that we
    created earlier::

        shutil.rmtree(dst_dir)

    Note that we don't have to remove the temporary ZIP archive, as that's done
    automatically for us by the tempfile module when the file is closed.

.. tab:: 英文

    We next have to clean up after ourselves by deleting the shapefile that we
    created earlier::

        shutil.rmtree(dst_dir)

    Note that we don't have to remove the temporary ZIP archive, as that's done
    automatically for us by the tempfile module when the file is closed.


将 ZIP 存档返回给用户
--------------------------------------------
Returning the ZIP archive to the user

.. tab:: 中文

    The last step in exporting the shapefile is to send the ZIP archive to the user's web
    browser so that it can be downloaded onto the user's computer. To do this, we'll
    create an HttpResponse object which includes a Django FileWrapper object to
    attach the ZIP archive to the HTTP response::

        f = FileWrapper(temp)
        response = HttpResponse(f, content_type="application/zip")
        response['Content-Disposition'] = \
                 "attachment; filename=" + shapefileName + ".zip"
        response['Content-Length'] = temp.tell()
        temp.seek(0)
        return response

    As you can see, we set up the HTTP response to indicate that we're returning a
    file attachment. This forces the user's browser to download the file rather than
    trying to display it. We also use the original shapefile's name as the name of the
    downloaded file.

    This completes the definition of the export_data() function. There's only one
    more thing to do: add the following import statements to the top of the exporter.
    py module::

        from django.http import HttpResponse
        from django.core.servers.basehttp import FileWrapper

    We've finally finished implementing the "Export Shapefile" feature. Test it out
    by running the server and clicking on the **Export** hyperlink beside one of your
    shapefiles. All going well, there'll be a slight pause and you'll be prompted to
    save your shapefile's ZIP archive to disk:

    .. image:: ./img/438-0.png
       :align: center

.. tab:: 英文

    The last step in exporting the shapefile is to send the ZIP archive to the user's web
    browser so that it can be downloaded onto the user's computer. To do this, we'll
    create an HttpResponse object which includes a Django FileWrapper object to
    attach the ZIP archive to the HTTP response::

        f = FileWrapper(temp)
        response = HttpResponse(f, content_type="application/zip")
        response['Content-Disposition'] = \
                 "attachment; filename=" + shapefileName + ".zip"
        response['Content-Length'] = temp.tell()
        temp.seek(0)
        return response

    As you can see, we set up the HTTP response to indicate that we're returning a
    file attachment. This forces the user's browser to download the file rather than
    trying to display it. We also use the original shapefile's name as the name of the
    downloaded file.

    This completes the definition of the export_data() function. There's only one
    more thing to do: add the following import statements to the top of the exporter.
    py module::

        from django.http import HttpResponse
        from django.core.servers.basehttp import FileWrapper

    We've finally finished implementing the "Export Shapefile" feature. Test it out
    by running the server and clicking on the **Export** hyperlink beside one of your
    shapefiles. All going well, there'll be a slight pause and you'll be prompted to
    save your shapefile's ZIP archive to disk:

    .. image:: ./img/438-0.png
       :align: center

