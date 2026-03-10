A custom library is a type of PL object in the database used for calling third-party library files.

Custom libraries are not suitable for ISC Distributed Cluster Deployment.

## Creating a Custom Library

Use the [CREATE LIBRARY](../../SQL Reference Manual/SQL Statements/CREATE LIBRARY) statement to create a custom library, defined by the following syntax:

**create library::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] LIBRARY plsql_library_source ";"
```

**plsql_library_source::=**

```ebnf+diagram
syntax::= [ schema "."] library_name ( IS | AS ) "'" full_path_name "'"
```

### 1. OR REPLACE

Rebuild the custom library if it already exists.

### 2. EDITIONABLE | NONEDITIONABLE

Used for syntax compatibility, with no practical meaning.

### 3. library\_name

The name of the custom library, which is mandatory and must comply with the YashanDB's [Object Naming Conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers).

### 4. 'full\_path\_name'

The complete path of the library file, which needs to be a string, supporting both absolute and relative paths. The length must be in the range of 0-255 bytes, such as '/home/yasdb/example/UDFexample.class'.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE OR REPLACE LIBRARY ya_lib IS
'/home/yasdb/example/UDFexample.class';
/
```

## Using a Custom Library

The custom library can be invoked in an external UDF using the call_spec clause; for detailed usage, see [External UDF](User-Defined Functions.html#ext_udf).

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