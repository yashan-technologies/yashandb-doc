This view shows a summary of undo-related statistics for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID              | INTEGER  | Group ID                                           |
| GROUP_NODE_ID         | INTEGER  | Node ID within the group                           |
| ID  | INTEGER | undo segment ID |
| BLK_REUSE             | INTEGER  | Number of times undo block is reused from blocklist |
| STEAL                 | INTEGER  | Number of attempts to steal expired blocks from other segments |
| DEGRADE_STEAL         | INTEGER  | Number of attempts to downgrade steal non-expired blocks from other segments |
| FORCE_STEAL           | INTEGER  | Number of attempts to force steal non-expired blocks from other segments |
| STEALED               | INTEGER  | Number of times expired blocks are stolen by other segments |
| DEGRADE_STEALED       | INTEGER  | Number of times non-expired blocks are downgraded and stolen by other segments |
| FORCE_STEALED         | INTEGER  | Number of times non-expired blocks are forcefully stolen by other segments |
| BALANCE_TIME          | DATE     | Time of the last background undo automatic balancing optimization |
| BALANCE               | INTEGER  | Number of times background undo automatic balancing optimization occurred |
| BALANCE_BLK           | INTEGER  | Total number of blocks returned to tablespace from segments during undo automatic balancing optimization |
| RECYCLE_TIME          | DATE     | Time of the last background recycling event        |
| RECYCLE_UFB           | INTEGER  | Number of background recycling of uninitialized blocks |
| RECYCLE_LIST          | INTEGER  | Number of background recycling of blocklists       |
| RECYCLE_LIST_BLK      | INTEGER  | Total number of blocks recycled from the blocklist on this segment during background recycling |
| RECYCLE_REQ           | INTEGER  | Number of times the segment initiated a recycling request |
| GET_RECYCLED_LIST     | INTEGER  | Number of times the segment recycled blocklists from other segments after initiating a recycling request |
| GET_LIST_BLK          | INTEGER  | Total number of blocks recalled from the blocklists of other segments after the segment initiated a recycling request |
| BLK\_APPEND | INTEGER | Count of Undo Block reuses from Free Array |
| BLK\_ALLOC | INTEGER| Number of Undo Block allocated from the UNDO tablespace|
| BLK\_REHANG | INTEGER | Number of Undo Block moved from Free Array to Blocklist |
| XACT\_AVG\_SIZE | BIGINT | Average UNDO Bytes consumed per Transaction |