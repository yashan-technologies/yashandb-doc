This view provides historical information about upgrades and installations performed on the database.

|Field |Type |Description |
| --- | --- |-----------------------------------------------------------------------------------------|
| ACTION_TIME | TIMESTAMP(6) | Timestamp of operation execution                                       |
| ACTION | VARCHAR(30) | Type of operation performed<br>\*   UPGRADE: Database Version Upgrade<br>\*   BOOTSTRAP: Database Initial Creation        |
| NAMESPACE | VARCHAR(30)   | Operation Namespace. This field is used to classify database operations and indicate the scope or object type affected by the operation<br>\*   DATAPATCH: It indicates that the operation affects the patch metadata, and this value is always used when ACTION is BOOTSTRAP<br>\*   SERVER: It indicates that the operation affects the core functions of the database server-side        |
| VERSION | VARCHAR(30)  | The version number of the component or database after the operation                 |
| ID  | NUMBER | Unique identifier               |
| COMMENTS | VARCHAR(255)  | Comment information related to the operation                               |
| BUNDLE_SERIES  | VARCHAR(30)  | Patch Package Series Information (Compatibility Fields)                       |