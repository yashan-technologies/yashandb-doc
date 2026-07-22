This view describes the partition key column information for all partition objects in the database.

|Field |Type |Description |
| --- |-------------| --- |
| OWNER              | VARCHAR(64)   | Owner of the partition object    |
| NAME               | VARCHAR(64)   | Name of the partition object     |
| OBJECT\_TYPE       | CHAR(5)       | Object type<br>\*   TABLE<br>\*   INDEX |
| COLUMN\_NAME       | VARCHAR(64)   | Column name                      |
| COLUMN\_POSITION    | INTEGER       | Position of the column in the partition key |
| COLLATED\_COLUMN\_ID | INTEGER      | COLUMN ID used internally        |