## Schema Introduction

A schema in a database is a logical container used to manage a set of relational objects (tables, indexes, etc.). During the database design phase, database administrators can group relational objects with strong business relevance into the same schema for unified management, while unrelated relational objects are placed into different schemas to avoid interference between different business objects. The relational objects contained in a schema are referred to as schema objects.

## Difference Between Schema and User

A schema is a logical container for schema objects. Its purpose is to facilitate the grouping and management of different objects, and each schema object must belong to a specific schema.

A user is an identity that performs specific operations in the database, primarily for privilege management. Each user has a schema that shares its name. Apart from the schema with the same name, a user can also access and use other schemas provided they have the corresponding privileges.

After a user logs into the database, the current schema (current_schema) defaults to the schema with the same name as the logged-in user. The current schema can be manually switched using the ALTER SESSION statement.

When a user executes an SQL statement that operates on a schema object, if the schema that the object belongs to is not explicitly specified, YashanDB will default to operating on the object in the current schema (errors will occur if the object does not exist or the user lacks operation privileges on the corresponding object).

For complex schema objects (such as views, stored procedures, and UDFs), during the compilation process of the definitions of such objects, YashanDB will temporarily switch the current user and current schema to the schema of the object being defined.

## Storage of Relational Objects

The information that needs to be stored for relational objects includes two parts: the definition and the data.

The definition is the information related to the object stored in YashanDB's system tables, which can be queried through system views, the DBMS_METADATA package, and other means.

The data of relational data structures (tables, indexes, AC, partitions) is stored by YashanDB in tablespaces. Objects stored in YashanDB tablespaces contain one or more logical storage structures called segments, which manage the data blocks (Block) belonging to the object. To improve efficiency, segments manage the database in batches using extents. When these objects are created, their storage tablespace must be specified. When these objects need to request storage space, the segment requests extents from the associated tablespace, which contain a contiguous group of data blocks. The objects can then write their data into a specific data block. When an object is deleted, the corresponding segment will be released, and the data blocks managed by those segments will be returned to the tablespace, which can then allocate them to other objects.

![](./image/table_storage.png)

## Dependency Relationships of Relational Objects

The dependency relationship of relational objects refers to the need to rely on or directly reference other relational objects when creating or using a certain relational object. YashanDB automatically detects and manages the state of other objects that a given object depends on when it is created, when its definition changes, or when it is used.

For example, if a view depends on a table and the data type of a column in that table changes, if the view references that column, the data type of the column in the view will also change accordingly.

If a stored procedure A calls another stored procedure B, after B is redefined, executing A again will make A call the redefined B.