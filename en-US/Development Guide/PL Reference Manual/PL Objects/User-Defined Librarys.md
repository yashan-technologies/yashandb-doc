A custom library is a type of PL object in the database used for calling third-party library files.

- In Standalone Primary-Standby or Primary-Standby Cluster Deployment, custom libraries can only be created, managed, and used on the primary database/primary cluster.

- Custom libraries are not suitable for ISC Distributed Cluster Deployment.

## Creating a Custom Library

Use the [CREATE LIBRARY](../../SQL Reference Manual/SQL Statements/CREATE LIBRARY) statement to create a custom library, defined by the following syntax:

**create library::=**

```ebnf
= CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] LIBRARY plsql_library_source ";".
```

**plsql_library_source::=**

```ebnf
= [ schema "."] library_name ( IS | AS ) "'" full_path_name "'".
```

### OR REPLACE

Rebuild the custom library if it already exists.

### EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no practical meaning.

### library\_name

The name of the custom library, which is mandatory and must comply with the YashanDB's [Object Naming Conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

### 'full\_path\_name'

The complete path of the library file, which needs to be a string, supporting both absolute and relative paths. The length must be in the range of 0-255 bytes, such as '/home/yasdb/example/UDFexample.class'.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE LIBRARY ya_lib IS
'/home/yasdb/example/UDFexample.class';
/
```

## Using a Custom Library

The custom library can be invoked in an external UDF using the call_spec clause; for detailed usage, see [External UDF](User-Defined Functions.md#ext_udf).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE FUNCTION udf_func(argu INT) RETURN VARCHAR IS
LANGUAGE java
NAME 'example.UDFexample.execJdbcexample(int) return string'
LIBRARY ya_lib;
/
```

## Deleting a Custom Library

Use the [DROP_LIBRARY](../../SQL Reference Manual/SQL Statements/DROP LIBRARY) statement to delete a custom library.