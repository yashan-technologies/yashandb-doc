This view displays all row access control policy information.

|Field |Type |Description |
| --- | --- | --- |
| POLICY_NAME     | VARCHAR(64)  | Policy name                                           |
| COLUMN_NAME     | VARCHAR(64)  | Label column name, the table protected by the policy will add a column with this name |
| STATUS          | VARCHAR(8)   | Status type<br>\*   ENABLE<br>\*   DISABLE             |
| POLICY_OPTIONS   | VARCHAR(256) | Policy control items                                  |
| POLICY_SUBSCRIBED | VARCHAR(5)  | Reserved parameter                                   |