The object privilege refers to the privilege that allows users to perform specific operations on specific objects. Narrowing the scope of privilege helps to control object access and ensure data security.

YashanDB supports object privileges that include various object operation privileges based on tables and [UDT](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Types).

The current system and all granted object privilege information can be viewed through the DBA_TAB_PRIVS view.

## Table-Level Object Privileges

Privileges can be defined for a table as shown in the table below:

|Privilege Name |Description |
| --- | --- |
| ALL PRIVILEGES     | Has all object privileges on the table.                       |
| INSERT             | Allows inserting data into the table.                        |
| SELECT             | Allows querying the table using SELECT statement.            |
| UPDATE             | Allows updating data in the table.                           |
| DELETE             | Allows deleting data from the table.                         |
| ALTER              | Allows altering the table, modifying its definition or attributes. |
| INDEX              | Allows creating an index for the table.                      |
| FLASHBACK          | Allows performing FLASHBACK operations on the table, including flashback queries (must also have query privilege) and flashback data. |
| READ               | Allows querying the table but cannot perform lock queries.   |

## UDT-Level Object Privileges

Privileges can be defined for a specific UDT as shown in the table below:

|Privilege Name |Description |
| --- | --- |
| ALL PRIVILEGES     | Has all object privileges on the type.                       |
| EXECUTE            | Allows the use of the specified UDT in scenarios such as table columns, variables, parameters, method calls, etc. |
| UNDER              | Allows creating a subtype based on the specified UDT.       |

Once the EXECUTE privilege of a specific UDT is granted to the PUBLIC role, all users will have the privilege to use that type, so please exercise caution when assigning privileges. In addition, UDT-level object privileges can only be granted to specified users and cannot be passed through role grants.