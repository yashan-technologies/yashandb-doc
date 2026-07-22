This view shows the audit types for objects.

|Field |Type |Description |
|---------------|-------------| --- |
| GROUP_ID       | NUMBER     | Group ID     |
| GROUP_NODE_ID  | NUMBER     | Node ID within the group |
| INST_ID        | NUMBER     | Instance ID  |
| ACTION         | INTEGER    | Object behavior ID |
| NAME          | VARCHAR(64) | Object behavior audit items: <br/>* ALL：All, including all subsequent object behavior audit items<br/>* SELECT：Perform query operations on the target object<br/>* INSERT：Insert data into the target object<br/>* UPDATE：Update the target object<br/>* DELETE：Delete data records from the target object<br/>* ALTER：Modify the target object<br/>* COMMENT：Add comments to the target object<br/>* GRANT：Grant target object-related privileges to users/roles<br/>* INDEX：Create indexes on the target object<br/>* LOCK：Lock the target object<br/>* EXECUTE：Execute the target object<br/>* FLASHBACK：Flashback the target object |
