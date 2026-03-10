The schema privilege refers to the management and control of all objects (such as tables, views, stored procedures, etc.) under a specific schema in the database. It is typically used to control user access and operation privileges for resources within that schema.

|Privilege Name |Description |
| --- | --- |
| ALL                | Has all privileges under a specific schema                                   |
| GRANT OPTION       | Has the privilege to grant the privileges owned under a specific schema      |
| SELECT             | Has the privilege to query any table under a specific schema                 |
| INSERT             | Has the privilege to insert data into any table under a specific schema      |
| UPDATE             | Has the privilege to update data in any table under a specific schema        |
| DELETE             | Has the privilege to delete data from any table under a specific schema      |
| CREATE             | Has the privilege to create any table under a specific schema                |
| DROP               | Has the privilege to delete any table under a specific schema                |
| ALTER              | Has the privilege to modify any table under a specific schema                |
| INDEX              | Has the privilege to create and drop any index under a specific schema       |
| CREATE VIEW        | Has the privilege to create any view under a specific schema                 |
| EXECUTE            | Has the privilege to execute any stored procedure under a specific schema     |
| CREATE ROUTINE     | Has the privilege to create a stored procedure under a specific schema       |
| ALTER ROUTINE      | Has the privilege to modify a stored procedure under a specific schema       |
| TRIGGER            | Has the privilege to operate triggers under a specific schema                |