This view displays the segment information of the global temporary table in the database session.

|Field |Type |Description |
| --- | --- | --- |
| SID         | SMALLINT  | Session ID                         |
| OBJ#        | BIGINT    | ID of the object to which the segment belongs |
| DATAOID     | BIGINT    | ID of the data object of the segment |
| BO#         | BIGINT    | ID of the parent object to which the segment belongs |
| ENTRY       | INTEGER   | Entry page of the segment         |
| SEGMENT_TYPE| VARCHAR(16)| Type of the object to which the segment belongs |