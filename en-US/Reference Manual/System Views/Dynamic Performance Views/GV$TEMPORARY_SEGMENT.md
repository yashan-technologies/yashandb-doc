This view displays the segment information of global temporary tables in the database session.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID      | NUMBER    | Group ID                            |
| GROUP_NODE_ID | NUMBER    | Node ID within the group            |
| INST_ID       | NUMBER    | Instance ID                         |
| SID           | SMALLINT  | Session ID                          |
| OBJ#          | BIGINT    | ID of the object to which the segment belongs |
| DATAOID       | BIGINT    | ID of the data object of the segment |
| BO#           | BIGINT    | Parent object ID of the object to which the segment belongs |
| ENTRY         | INTEGER    | Entry page of the segment           |
| SEGMENT_TYPE  | VARCHAR(16) | Type of the object to which the segment belongs |