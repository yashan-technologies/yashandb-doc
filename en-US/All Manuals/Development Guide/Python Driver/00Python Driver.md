YashanDB Python driver (python-yasdb) is a Python extension module that supports the [Python DB API specification](https://peps.python.org/pep-0249/), allowing general Python applications to connect directly to the YashanDB database.

This driver supports connections only when YashanDB is deployed in yashan mode.

The YashanDB product installation package provides two Python driver packages: yasdb and yaspy. Developers can install either one to connect to the YashanDB database for access operations (starting from YashanDB 23.2, no further functionality extensions will be made to the yaspy module; users are advised to install and use the yasdb module). The content listed in this chapter uses yasdb as an example, while the yaspy module can be referenced.

The module includes the following components:

- connect(): A function to create a database connection.
- Globals: Variables defined within the module.
- Connection: The connection object from the Python application to the database.
- Cursor: The cursor object created after connecting to the database.

For a detailed description of the above content, see the [Status of Support for YashanDB Python Driver](Status of Support for YashanDB Python Driver/00Status of Support for YashanDB Python Driver) chapter.

For specific installation steps, refer to the description in the [YashanDB Python Driver Installation](YashanDB Python Driver Installation) chapter.

To learn about the simple process of connecting and operating YashanDB within a Python application, check the [YashanDB Python Driver Usage Introduction](YashanDB Python Driver Usage Introduction) chapter.

This manual also provides simple examples of using the YashanDB Python driver, see [YashanDB Python Driver Usage Examples](YashanDB Python Driver Usage Examples) for details.

For data type conversion between Python and YashanDB, see the description in the [Data Type Mapping Relationships in YashanDB Python Driver](Data Type Mapping Relationships in YashanDB Python Driver) chapter.