设置数据库
============================================

Setting up the database

.. tab:: 中文

    Assuming you have created a PostgreSQL template for PostGIS as described in
    the Prerequisites section of this chapter, setting up the PostGIS database for the
    ShapeEditor is trivial—simply type the following at the command prompt::

        % createdb shapeeditor

    .. note::

        If you don't have PostgreSQL's createdb command on your path, you will need to prefix this command with the directory where PostgreSQL's command-line tools are stored.

    If your PostgreSQL installation requires you to supply a username when creating
    a database, you can do this by adding the -U command-line option, like this::

        % createdb shapeeditor -U <username>
    
    You will be prompted to enter the user's password, if it has one.

    This will create a new database named shapeeditor, which we will use to hold
    the data for our ShapeEditor project.

    All going well, you should now have a database named shapeeditor on your
    computer. Open up a command-line client to this database by typing::

        % psql shapeeditor

    .. note::

        You'll need to add a -U command-line option if your PostgreSQL installation requires it.

    You should see the PostgreSQL command line prompt::

        psql (9.1.6)
        Type "help" for help.
        shapeeditor=#

    We now need to spatially-enable this database, by installing the PostGIS extension. To do this, type::
    
        CREATE EXENSION postgis;

    If you then type \d and press Return, you should see a list of the tables in your new PostGIS database::

        List of relations
        Schema |       Name        | Type | Owner
        --------+-------------------+----------+-------
        public | geography_columns | view | user
        public | geometry_columns  | view | user
        public | raster_columns    | view | user
        public | raster_overviews  | view | user
        public | spatial_ref_sys   | table| user
        (5 rows)

    These five tables are installed automatically by the PostGIS extension. To leave the
    PostgreSQL command-line client, type \q and then press Return::

        geodjango=# \q
        %

    Congratulations! You have just set up a PostGIS database for the ShapeEditor
    application to use.

.. tab:: 英文

    Assuming you have created a PostgreSQL template for PostGIS as described in
    the Prerequisites section of this chapter, setting up the PostGIS database for the
    ShapeEditor is trivial—simply type the following at the command prompt::

        % createdb shapeeditor

    .. note::

        If you don't have PostgreSQL's createdb command on your path, you will need to prefix this command with the directory where PostgreSQL's command-line tools are stored.

    If your PostgreSQL installation requires you to supply a username when creating
    a database, you can do this by adding the -U command-line option, like this::

        % createdb shapeeditor -U <username>
    
    You will be prompted to enter the user's password, if it has one.

    This will create a new database named shapeeditor, which we will use to hold
    the data for our ShapeEditor project.

    All going well, you should now have a database named shapeeditor on your
    computer. Open up a command-line client to this database by typing::

        % psql shapeeditor

    .. note::

        You'll need to add a -U command-line option if your PostgreSQL installation requires it.

    You should see the PostgreSQL command line prompt::

        psql (9.1.6)
        Type "help" for help.
        shapeeditor=#

    We now need to spatially-enable this database, by installing the PostGIS extension. To do this, type::
    
        CREATE EXENSION postgis;

    If you then type \d and press Return, you should see a list of the tables in your new PostGIS database::

        List of relations
        Schema |       Name        | Type | Owner
        --------+-------------------+----------+-------
        public | geography_columns | view | user
        public | geometry_columns  | view | user
        public | raster_columns    | view | user
        public | raster_overviews  | view | user
        public | spatial_ref_sys   | table| user
        (5 rows)

    These five tables are installed automatically by the PostGIS extension. To leave the
    PostgreSQL command-line client, type \q and then press Return::

        geodjango=# \q
        %

    Congratulations! You have just set up a PostGIS database for the ShapeEditor
    application to use.
