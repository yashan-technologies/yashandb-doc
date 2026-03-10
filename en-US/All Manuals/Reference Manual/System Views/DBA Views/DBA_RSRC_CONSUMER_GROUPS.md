This view displays information about all resource consumer groups in the database.

|Field |Type |Description |
| --- | --- | --- |
| CONSUMER_GROUP_ID | BIGINT       | Resource consumer group ID     |
| CONSUMER_GROUP     | VARCHAR(64)  | Name of the resource consumer group |
| CPU_METHOD         | VARCHAR(1)   | For compatibility only, currently fixed as `NULL` |
| MGMT_METHOD        | VARCHAR(1)   | For compatibility only, currently fixed as `NULL` |
| INTERNAL_USE       | VARCHAR(1)   | For compatibility only, currently fixed as `NULL` |
| COMMENTS            | VARCHAR(2000)| Comments about the resource consumer group |
| CATEGORY            | VARCHAR(1)   | For compatibility only, currently fixed as `NULL` |
| STATUS              | VARCHAR(1)   | For compatibility only, currently fixed as `NULL` |
| MANDATORY           | VARCHAR(1)   | For compatibility only, currently fixed as `NULL` |