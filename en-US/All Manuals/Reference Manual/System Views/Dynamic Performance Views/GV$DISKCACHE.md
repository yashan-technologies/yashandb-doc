This view displays the status information of the disk buffer.

|Field |Type |Description |
|:----------------------|:----------|:---------------------------|
| GROUP_ID              | NUMBER    | Group ID                     |
| GROUP_NODE_ID         | NUMBER    | Node ID within the group     |
| INST_ID               | NUMBER    | Instance ID                  |
| REGION_ID             | INTEGER   | Region ID of the disk buffer |
| REGION_GROUP_ID       | INTEGER   | Group ID where the region of the disk buffer is located |
| REGION_HIT            | INTEGER   | Total hit count of the disk buffer domain |
| ITEM_NUM              | INTEGER   | Number of buffer objects in the disk buffer domain |
| REGION_LOAD_TIMESTAMP | TIMESTAMP | SCN loading start of the disk buffer domain |