This view displays information about the enabled audit policies.

|Field |Type |Description |
| --- | --- | --- |
| POLICY_NAME   | VARCHAR(64)  | Audit policy name                                |
| ENABLED_OPTION| VARCHAR(15)  | Enabled option of the audit policy<br>\* BY USER: targeted users <br>\* EXCEPT USER: excluded users |
| ENTITY_NAME   | VARCHAR(64)  | User or role targeted by the audit policy; if targeting all users, the value is "ALL USERS" |
| ENTITY_TYPE   | VARCHAR(7)   | Type targeted by the audit policy<br>\* USER    |
| SUCCESS       | VARCHAR(3)   | "YES" indicates auditing of successful events; "NO" indicates no auditing of successful events |
| FAILURE       | VARCHAR(3)   | "YES" indicates auditing of failed events; "NO" indicates no auditing of failed events |