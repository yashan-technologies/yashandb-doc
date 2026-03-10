This view displays a summary of undo-related statistics.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER  | Instance ID |
| ID  | INTEGER | undo segment ID |
| BLK_REUSE | INTEGER | Number of times undo blocks are reused from the blocklist |
| STEAL | INTEGER | Number of attempts to steal expired blocks from other segments |
| DEGRADE_STEAL | INTEGER | Number of attempts to downgrade steal unexpired blocks from other segments |
| FORCE_STEAL | INTEGER | Number of attempts to forcibly steal unexpired blocks from other segments |
| STEALED | INTEGER | Number of expired blocks stolen by other segments |
| DEGRADE_STEALED | INTEGER | Number of unexpired blocks downgraded and stolen by other segments |
| FORCE_STEALED | INTEGER | Number of unexpired blocks forcibly stolen by other segments |
| BALANCE_TIME | DATE | Time of the last automatic background undo balancing optimization |
| BALANCE | INTEGER | Number of background undo automatic balancing optimizations that occurred |
| BALANCE_BLK | INTEGER | Total number of blocks returned to the tablespace from segments during automatic undo balancing optimization |
| RECYCLE_TIME | DATE | Time of the last background reclamation |
| RECYCLE_UFB | INTEGER | Number of times uninitialized blocks were reclaimed in the background |
| RECYCLE_LIST | INTEGER | Number of times the blocklist was reclaimed in the background |
| RECYCLE_LIST_BLK | INTEGER | Total number of blocks reclaimed from the blocklist of this segment in the background |
| RECYCLE_REQ | INTEGER | Number of reclamation requests initiated by the segment |
| GET_RECYCLED_LIST | INTEGER | Number of times the segment requested reclamation and obtained the blocklist from other segments |
| GET_LIST_BLK | INTEGER | Total number of blocks reclaimed from the blocklist of other segments after the segment initiated the reclamation request |
| BLK_APPEND | INTEGER | Count of Undo Block reuses from Free Array |
| BLK_ALLOC | INTEGER| Number of Undo Block allocated from the UNDO tablespace|
| BLK_REHANG | INTEGER | Number of Undo Block moved from Free Array to Blocklist |
| XACT_AVG_SIZE | BIGINT | Average UNDO Bytes consumed per Transaction |
