This view displays the metrics of background async prefetch queues.

|Field |Type |Description |
|----------------------------|---------------|---------------------------------------------------------|
| ID | INTEGER | Async Prefetch Queue ID |
| REQUEST_TIME | BIGINT | Time taken by the async queue to receive prefetch requests when issuing async prefetch requests |
| REQUEST_COUNT | BIGINT | Number of prefetch requests received by the async queue when issuing async prefetch requests |
| REQUEST_BLOCK_COUNT | BIGINT | Total number of blocks in received prefetch requests |
| QUEUE_HEAD | INTEGER | Slot ID at the head of the async queue |
| QUEUE_TAIL | INTEGER | Slot ID at the tail of the async queue |
| QUEUE_SORT_TAIL | INTEGER | Slot ID at the sorted tail of the async queue |
| QUEUE_LEN | INTEGER | Length of the async queue |