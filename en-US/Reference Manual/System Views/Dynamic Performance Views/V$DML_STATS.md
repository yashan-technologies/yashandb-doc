This view shows the changes made to the tables in memory since the last statistics collection, which is used for the implementation of internal mechanisms. If users need to check the changes of tables, please use the USER_TAB_MODIFICATIONS view.

|Field |Type |Description |
| --- | --- | --- |
| OBJ#       | BIGINT  | Object ID                            |
| BASE_OBJ#  | BIGINT  | Parent Object ID                     |
| INSERTS    | BIGINT  | Number of inserted rows              |
| UPDATES    | BIGINT  | Number of updated rows               |
| DELETES    | BIGINT  | Number of deleted rows               |
| CURR_ROWS  | BIGINT  | Number of rows obtained from the last statistics collection |
| LAST_MODIFY | DATE    | Time of the last modification        |
| FLAGS      | INTEGER | Flag field indicating whether it has been truncated |
| DROP_SEGS  | BIGINT  | Number of times the segment has been truncated |