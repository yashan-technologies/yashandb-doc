This view displays a summary of undo-related statistics.

|Field |Type |Description |
| --- | --- | --- |
| ID  | INTEGER | undo segment ID |
| BLK\_REUSE | INTEGER | Number of times an undo block was reused from the blocklist |
| STEAL | INTEGER | Number of attempts to steal expired blocks from other segments |
| DEGRADE\_STEAL | INTEGER | Number of attempts to downgrade steal unexpired blocks from other segments |
| FORCE\_STEAL | INTEGER | Number of attempts to forcibly steal unexpired blocks from other segments |
| STEALED | INTEGER | Number of expired blocks stolen by other segments |
| DEGRADE\_STEALED | INTEGER | Number of unexpired blocks downgraded and stolen by other segments |
| FORCE\_STEALED | INTEGER | Number of unexpired blocks forcibly stolen by other segments |
| BALANCE\_TIME | DATE | Time of the last automatic background undo balance optimization |
| BALANCE | INTEGER | Number of occurrences of automatic background undo balance optimization |
| BALANCE\_BLK | INTEGER | Total number of blocks returned to the tablespace from the segment during automatic undo balance optimization |
| RECYCLE\_TIME | DATE | Time of the last background recycling event |
| RECYCLE\_UFB | INTEGER | Number of uninitialized blocks recycled by the background |
| RECYCLE\_LIST | INTEGER | Number of times the blocklist was recycled by the background |
| RECYCLE\_LIST\_BLK | INTEGER | Total number of blocks recycled during the background recycling of the blocklist in this segment |
| RECYCLE\_REQ | INTEGER | Number of recycling requests initiated by the segment |
| GET\_RECYCLED\_LIST | INTEGER | Number of times the segment requested to recycle the blocklist from other segments after initiating the recycling request |
| GET\_LIST\_BLK | INTEGER | Number of blocks received from the blocklist of other segments after the segment initiated the recycling request |
| BLK\_APPEND | INTEGER | Count of Undo Block reuses from Free Array |
| BLK\_ALLOC | INTEGER| Number of Undo Block allocated from the UNDO tablespace|
| BLK\_REHANG | INTEGER | Number of Undo Block moved from Free Array to Blocklist |
| XACT\_AVG\_SIZE | BIGINT | Average UNDO Bytes consumed per Transaction |