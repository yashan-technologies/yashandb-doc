This view describes the partition key column information for all partition objects in the database.

|Field |Type |Description |
| --- |-------------| --- |
| OWNER              | VARCHAR(64)   | Owner of the partition object    |
| NAME               | VARCHAR(64)   | Name of the partition object     |
| OBJECT_TYPE       | CHAR(5)       | Object type<br>\*   TABLE<br>\*   INDEX |
| COLUMN_NAME       | VARCHAR(64)   | Column name                      |
| COLUMN_POSITION    | INTEGER       | Position of the column in the partition key |
| COLLATED_COLUMN_ID | INTEGER      | COLUMN ID used internally        |