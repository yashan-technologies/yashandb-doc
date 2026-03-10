This view shows the changes in tables recorded in memory since the last statistics collection, used for the implementation of internal mechanisms. If users need to check the changes in tables, please use the USER_TAB_MODIFICATIONS view.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER  | Group ID                     |
| GROUP_NODE_ID| NUMBER  | Node ID within the group     |
| INST_ID      | NUMBER  | Instance ID                  |
| OBJ#         | BIGINT  | Object ID                    |
| BASE_OBJ#    | BIGINT  | Parent Object ID             |
| INSERTS      | BIGINT  | Number of inserted rows      |
| UPDATES      | BIGINT  | Number of updated rows       |
| DELETES      | BIGINT  | Number of deleted rows       |
| CURR_ROWS    | BIGINT  | Number of rows obtained in the last statistics collection |
| LAST_MODIFY  | DATE    | Time of the last change      |
| FLAGS        | INTEGER | Flag field, indicates whether it has been truncated |
| DROP_SEGS    | BIGINT  | Number of times the segment has been truncated |