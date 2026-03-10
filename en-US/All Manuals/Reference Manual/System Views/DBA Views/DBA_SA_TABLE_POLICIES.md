This view displays information about the row access control policies set on all tables.

|Field |Type |Description |
| --- | --- | --- |
| POLICY_NAME    | VARCHAR(64)  | Policy name                   |
| SCHEMA_NAME    | VARCHAR(64)  | Username                      |
| TABLE_NAME     | VARCHAR(64)  | Table name                    |
| STATUS         | VARCHAR(8)   | Status type<br>\*   ENABLE<br>\*   DISABLE |
| TABLE_OPTIONS   | VARCHAR(4000) | Control items applied to the table |
| FUNCTION       | VARCHAR(1024) | Reserved parameter            |
| PREDICATE      | VARCHAR(256)  | Reserved parameter            |