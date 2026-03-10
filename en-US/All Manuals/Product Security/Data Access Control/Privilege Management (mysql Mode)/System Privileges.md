System privileges are privileges that allow users to perform certain system-level operations. They may apply to operations related to a certain class of objects or to operations related to the entire database. The functionality of system privileges is extensive, so please grant them with caution. Generally, system privileges are granted only by administrators to ensure that the authorized users are trustworthy.

|Privilege Name |Description |
| --- | --- |
| ALL                 | Has all system privileges                                          |
| CREATE USER         | Has the privilege to create, modify, and delete users            |
| GRANT OPTION        | Has the privilege to grant owned privileges                        |
| FILE                | Has the privilege to execute LOAD DATA statement for imports      |
| SELECT              | Has the privilege to query any table                              |
| INSERT              | Has the privilege to insert data into any table                   |
| UPDATE              | Has the privilege to update data in any table                     |
| DELETE              | Has the privilege to delete data from any table                   |
| CREATE              | Has the privilege to create any table                             |
| DROP                | Has the privilege to delete any table                             |
| ALTER               | Has the privilege to modify any table                             |
| INDEX               | Has the privilege to create and delete any index                  |
| CREATE VIEW         | Has the privilege to create any view                              |
| EXECUTE             | Has the privilege to execute any stored procedure                 |
| CREATE ROUTINE      | Has the privilege to create stored procedures                     |
| ALTER ROUTINE       | Has the privilege to modify stored procedures                     |
| TRIGGER             | Has the privilege for trigger operations                          |
| CREATE TABLESPACE   | Has the privilege to create tablespaces                           |