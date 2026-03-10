This view displays information about all flashback restore points in the entire database.

It is applicable only to Standalone Deployment; this view is meaningless in other deployment forms.

|Field |Type |Description |
| --- | --- | --- |
| NAME          | VARCHAR(68) | Restore point name                   |
| SCN           | BIGINT      | The SCN that the restore point represents for flashback |
| IS_GUARANTEE  | TINYINT     | Whether it is a permanent restore point<br/>* 0: No<br/>* 1: Yes |
| CREATE_TIME   | TIMESTAMP   | The timestamp corresponding to when the restore point was created |