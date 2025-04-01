设置数据库
============================================

Setting up the database

.. tab:: 中文

    假设您已经按照本章的“先决条件”部分所述创建了一个用于 PostGIS 的 PostgreSQL 模板，那么为 ShapeEditor 设置 PostGIS 数据库就非常简单 —— 只需在命令提示符下输入以下内容：

        % createdb shapeeditor

    .. note::

        如果您的系统路径中没有 PostgreSQL 的 `createdb` 命令，您需要在该命令前加上 PostgreSQL 命令行工具所在的目录。

    如果您的 PostgreSQL 安装在创建数据库时要求提供用户名，您可以通过添加 `-U` 命令行选项来指定用户名，如下所示：

        % createdb shapeeditor -U <username>

    如果该用户有密码，系统将提示您输入密码。

    这将创建一个名为 `shapeeditor` 的新数据库，我们将使用它来存储 ShapeEditor 项目的数据。

    一切顺利的话，您的计算机上现在应该有一个名为 `shapeeditor` 的数据库了。通过输入以下命令打开该数据库的命令行客户端：

        % psql shapeeditor

    .. note::

        如果您的 PostgreSQL 安装需要指定用户名，您需要添加 `-U` 命令行选项。

    您应该会看到 PostgreSQL 的命令行提示符：

        psql (9.1.6)
        Type "help" for help.
        shapeeditor=#

    现在我们需要通过安装 PostGIS 扩展来为这个数据库启用空间功能。为此，请输入：

        CREATE EXTENSION postgis;

    如果接着输入 `\d` 并按下回车键，您应该会看到新创建的 PostGIS 数据库中的表列表：

        List of relations
        Schema |       Name        | Type | Owner
        --------+-------------------+----------+-------
        public | geography_columns | view | user
        public | geometry_columns  | view | user
        public | raster_columns    | view | user
        public | raster_overviews  | view | user
        public | spatial_ref_sys   | table| user
        (5 rows)

    这五个表是由 PostGIS 扩展自动安装的。要退出 PostgreSQL 命令行客户端，请输入 `\q` 然后按下回车键：

        geodjango=# \q
        %

    恭喜！您刚刚为 ShapeEditor 应用程序设置好了 PostGIS 数据库。

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
