This view displays the status information of the disk buffer.

|Field |Type |Description |
|:----------------------|:----------|:---------------------------|
| REGION_ID             | INTEGER   | The region ID of the disk buffer |
| REGION_GROUP_ID       | INTEGER   | The group ID of the region of the disk buffer |
| REGION_HIT            | INTEGER   | The total hit count of the disk buffer region |
| ITEM_NUM              | INTEGER   | The number of buffer objects in the disk buffer region |
| REGION_LOAD_TIMESTAMP | TIMESTAMP | The SCN when the disk buffer region started loading |