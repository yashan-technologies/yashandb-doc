This view displays the statistics related to the ASH buffer.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID                 | NUMBER      | Group ID                                             |
| GROUP\_NODE\_ID           | NUMBER      | Node ID within the group                             |
| INST\_ID                  | NUMBER      | Instance ID                                          |
| TOTAL_SIZE                | BIGINT      | Total ASH buffer size, in bytes                      |
| FIXED_SIZE                | BIGINT      | Fixed ASH buffer size, in bytes                      |
| SAMPLING_INTERVAL         | BIGINT      | Sampling interval, in milliseconds                  |
| OLDEST_SAMPLE_ID          | BIGINT      | Oldest sample ID in the buffer                       |
| OLDEST_SAMPLE_TIME        | TIMESTAMP(6)   | Oldest sample timestamp                              |
| LATEST_SAMPLE_ID          | BIGINT      | Latest sample ID                                     |
| LATEST_SAMPLE_TIME        | TIMESTAMP(6)   | Latest sample timestamp                              |
| SAMPLE_COUNT              | BIGINT      | Total number of samples                              |
| DROPPED_SAMPLE_COUNT      | BIGINT      | Number of dropped samples                            |
| SAMPLED_BYTES             | BIGINT      | Total size of sampled data, in bytes                  |
| SAMPLER_ELAPSED_TIME      | BIGINT      | Total sampling elapsed time, in microseconds          |
| DISK_FILTER_RATIO         | INTEGER     | Ratio of memory samples to disk samples              |
| LATEST_FLUSH_SAMPLE_ID    | BIGINT      | Latest flushed sample ID                             |
| AWR_FLUSH_BYTES           | BIGINT      | Total flushed data size, in bytes                    |
| AWR_FLUSH_ELAPSED_TIME    | BIGINT      | Total flush elapsed time, in microseconds            |
| AWR_FLUSH_COUNT           | BIGINT      | Total number of flushes                              |
| AWR_FLUSH_EMERGENCY_COUNT | BIGINT      | Number of emergency flushes                         |
| CON_ID                    | INTEGER     | Current tenant ID (reserved field)                  |