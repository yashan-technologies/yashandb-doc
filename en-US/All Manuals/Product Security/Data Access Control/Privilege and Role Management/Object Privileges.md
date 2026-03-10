The object privilege refers to the privilege that allows users to perform specific operations on specific objects. Narrowing the scope of privilege helps to control object access and ensure data security.

YashanDB supports object privileges including various object operation permissions based on tables and certain [PL objects](../../../开发手册/PL参考手册/PL对象/00PL对象) (including stored procedures, advanced packages, UDFs, and UDTs).

The current system and all granted object privilege information can be viewed through the DBA_TAB_PRIVS view.

Various object privileges are shown in the table below.

|Privilege Name |Description |
| --- | --- |
| ALL PRIVILEGES     | Has all object privileges on the object (such as a table or a UDT).                       |
| **Tables**:     |         |
| INSERT             | Allows inserting data into the table.                        |
| SELECT             | Allows querying the table using SELECT statement.            |
| UPDATE             | Allows updating data in the table.                           |
| DELETE             | Allows deleting data from the table.                         |
| ALTER              | Allows altering the table, modifying its definition or attributes. |
| INDEX              | Allows creating an index for the table.                      |
| FLASHBACK          | Allows performing FLASHBACK operations on the table, including flashback queries (must also have query privilege) and flashback data. |
| READ               | Allows querying the table but cannot perform lock queries.   |
| **PL Objects**：        |          |
| EXECUTE            | Allows using/executing a PL object, including stored procedures, advanced packages, UDFs, or UDTs. For example, using a UDT in scenarios such as table columns, variables, parameters, or method calls.   |
| UNDER     | Allows creating a subtype based on the specified UDT.       |