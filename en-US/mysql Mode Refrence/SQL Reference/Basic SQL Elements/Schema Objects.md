
In YashanDB yashan mode, each user owns a Schema with the same name, and when logging in, the user connects to the corresponding Schema; however, in mysql mode, users need to create and use Schema through CREATE DATABASE and USE Database_name statements.

For developers, all access and operations on the database are based on various objects located under the specified schema. Schema objects include:

- Tables
- AC
- Indexes
- Partitions
- Views
- Sequences
- Synonyms
- Custom data types
- UDFs
- Stored procedures
- Packages
- Scheduled tasks
- Triggers

For more details, please refer to [schema object](../../../All Manuals/Product Concepts/Relational Data Structure/Schema Objects/00Schema Objects).
