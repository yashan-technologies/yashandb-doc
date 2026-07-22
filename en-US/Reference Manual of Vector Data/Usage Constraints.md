## Usage Constraints

When using Yashan vector database, the following constraints apply:

|Constraint Item |Constraint Behavior |
| ----------------------- |-------------------------------------------|
| Deployment Mode | Supports standalone (Primary-Standby) deployment  |
| Syntax Mode | Used in yashan mode. |
| Table Type | HEAP type |
| Data Operations | Supports INSERT, UPDATE and DELETE operations on vector data via SQL statements |
| INDEX|Support HNSW index|
| PL Support for Vectors | - Supports user-defined functions and stored procedures that accept and return vector types. The size of vector types in stored procedures must not exceed 65534 bytes.<br>- Vector types are not allowed in user-defined types or triggers. |
| Data Import/Export | - Import: Supports batch import of vector data via yasldr.<br>- Export: Not supported. |
| Driver Interface | C driver and Python driver are supported |
| Operations and Maintenance Management | Operations such as instance management, file management, high availability, backup, and recovery are consistent with the general management operations of the Yashan Database |
| Data Migration | Migration of vector tables via Ystream or YMP is not supported |


