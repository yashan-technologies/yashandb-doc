*exp* is the companion export tool of YashanDB, providing users with the ability to export data in various ways, enabling data migration or backup recovery:

- [Metadata Files Export](Metadata Files Export): Exports all metadata and data from the database, including table structures, indexes, and constraints. The generated metadata file can be imported into the database (homogeneous) using the [imp](../imp/00imp) tool.
- [CSV Files Export](CSV Files Export): Exports data arranged according to the specified table structure from the database, and specifies the generation of a CSV file using the --csv command option. The generated CSV file can be imported into the database (homogeneous or heterogeneous) using the [yasldr](../yasldr/00yasldr) tool or the [LOAD DATA](../../Development Guide/SQL Reference Manual/SQL Statements/LOAD DATA) statement.
- [SQL Files Export](SQL Files Export): Exports all metadata from the database, including table structures, indexes, and constraints, and specifies the generation of an SQL file using the --sql command option. The generated SQL file can be imported into the database (homogeneous) using the [yasql](../yasql/00yasql) tool.

## Metadata File Export

Supports various export modes such as FULL (full database export), OWNER (exporting a specific user), TABLES (exporting specified tables).

The objects that can be exported through the *exp* tool include:

- User
- SEQUENCE
- AC
- Synonym
- View/Materialized View: Materialized views must be exported in FULL or OWNER mode.
- PACKAGE(BODY)/PROCEDURE/FUNCTION/TRIGGER/LIBRARY/TYPE(BODY)
- Table (excluding tables with virtual columns)
- Index/Partitioned Index
- Primary Key
- Foreign Key
- Audit Policies/Enabling: Can only be exported in FULL mode, and audit logs cannot be exported.
- Privilege
- PROFILE
- OUTLINE
- Scheduled Tasks
- DATABASE LINK

Moreover, the object's constraints, dependencies (e.g., TABLEs that the VIEW depends on), column properties, and other information will also be exported.

> **Note**:
> 
> - For tables with UDT columns, the following restrictions apply to metadata exports:
>   - For tables that reference TYPE, only the definition of the table is supported for export; data within the table cannot be exported.
>   - For tables that use TYPE as a column synonym, it will be exported as a specific type.
>   - In OWNER and TABLES modes, indirectly used TYPE and TYPEs from other users cannot be exported.
> - For tables with encrypted columns, only the metadata of the table can be exported; data within the table cannot be exported.

## CSV File Export

Supports exporting data from one or more specified tables, storing it in CSV format in the data file.

> **Note**:
> 
> - For char type data, when exporting to CSV, trailing spaces will be removed before export.
> - For encrypted columns, data cannot be specified to be exported as binary strings.

## SQL File Export

Supports exporting metadata SQL for specified tables, users, or the entire database.

> **Note**:
>
> The export scope covers all objects' metadata exported in the metadata data file, excluding the export of object privileges.

## Privilege Requirements

The execution command of the *exp* tool requires the input of the database user and password.

When exporting metadata, this user is the executing user for the export operation, not the export object. The privilege rules for this operating user are:

- No privilege is required when exporting objects under this user.
- When exporting objects under other users, the DBA role privilege must be held.

***Example***

```sql
-- Grant DBA role to the sales user
GRANT DBA TO sales;

-- Export sales0 user
exp sales/sales file=export.dump owner=sales0

-- Export tables under the sales0 user
exp sales/sales file=export.dump tables=sales0.area
```

When exporting CSV data, this user must be the SYS user or another user with DBA privilege.

> **Note**: 
>
> Higher version *exp* tools do not currently support connecting to lower version databases for export. Please use version-compatible tools for data migration operations.