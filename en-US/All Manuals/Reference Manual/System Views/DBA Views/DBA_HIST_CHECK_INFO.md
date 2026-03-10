This view shows information about accessible enabled tamper-proof objects.

|Field |Type |Description |
|-------------|-------------|---------------------------------------------------------------------|
| OWNER        | VARCHAR(64)  | The username of the table                                             |
| TABLE_NAME  | VARCHAR(64)  | Table name                                                           |
| MODE         | VARCHAR(8)   | Tamper-proof mode (currently only for read-only table mode)<br/>"READONLY" indicates read-only table mode<br/>"UNKNOWN" indicates unknown mode |
| ACHECK       | VARCHAR(5)   | Whether the tamper-proof table has been tampered with:<br/>"TRUE" indicates not tampered<br/>"FALSE" indicates tampered |