This view displays all tables that have been configured with row-level access control, along with their corresponding policy information.

|Field |Type |Description |
| --- | --- | --- |
| POLICY_NAME    | VARCHAR(64)   | The name of the LBAC label policy          |
| SCHEMA_NAME    | VARCHAR(64)  | The owner of the table |
| TABLE_NAME     | VARCHAR(64)  | The name of the table   |
| STATUS          | VARCHAR(8)   | The status of the LBAC label policy, which is currently always ENABLE |
| TABLE_OPTIONS   | VARCHAR(4000) | The mandatory control option(s) applied to this table by the policy |
| FUNCTION       | VARCHAR(1024) | Reserved parameter            |
| PREDICATE      | VARCHAR(256)  | Reserved parameter            |