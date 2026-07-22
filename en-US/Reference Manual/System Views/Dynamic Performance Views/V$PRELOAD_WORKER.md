This view displays the execution status of background async prefetch threads.

|Field |Type |Description |
|----------------------------|---------------|---------------------------------------------------------|
| ID | INTEGER | Prefetch Thread ID |
| SORT_TIME | BIGINT | Time spent by the prefetch thread sorting the Block request queue during async prefetch operations |
| SORT_COUNT | BIGINT | Number of times the prefetch thread sorted the Block request queue |
| POP_TIME | BIGINT | Time taken by the prefetch thread to dequeue requests from the prefetch queue |
| POP_COUNT | BIGINT | Number of times the prefetch thread dequeued requests |
| READ_TIME | BIGINT | I/O time spent by the prefetch thread reading data files |
| READ_COUNT | BIGINT | Number of I/O operations performed by the prefetch thread when reading data files |
| READ_BLOCK_COUNT | BIGINT | Total number of pages read from data files by the prefetch thread |
| LOAD_TIME | BIGINT | Time taken by the prefetch thread to load all Blocks in the prefetch queue |
| LOAD_BLOCK_COUNT | BIGINT | Number of Blocks loaded by the prefetch thread from the prefetch queue |
| FAIL_COUNT | BIGINT | Number of failed load operations by the prefetch thread |
| SKIP_COUNT | BIGINT | Number of outdated prefetch loads skipped |
| LAST_QUEUE | INTEGER | Queue ID of the last prefetch task executed by the async prefetch thread |