This view displays information about all flashback restore points in the entire database.



This feature is not available in ISC Distributed Cluster Deployment, and this view is meaningless.



|Field |Type |Description |
| --- | --- | --- |
| NAME          | VARCHAR(68) | Restore point name                   |
| SCN           | BIGINT      | The SCN that the restore point represents for flashback |
| IS_GUARANTEE  | TINYINT     | Whether it is a permanent restore point<br/>* 0: No<br/>* 1: Yes |
| CREATE_TIME   | TIMESTAMP(6)   | The TIMESTAMP corresponding to when the restore point was created |
