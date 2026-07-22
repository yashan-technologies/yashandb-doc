This view displays ASH buffer-related statistics.

| Field                        | Type          | Description                                                  |
|---------------------------|-------------|-----------------------------------------------------|
| TOTAL_SIZE                | BIGINT      | Total ASH buffer size (unit: bytes)                                                |
| FIXED_SIZE                | BIGINT      | Fixed ASH buffer size (unit: bytes)                                                |
| SAMPLING_INTERVAL         | BIGINT      | Sampling interval in milliseconds                                                |
| OLDEST_SAMPLE_ID          | BIGINT      | Oldest sample ID in the buffer                                                |
| OLDEST_SAMPLE_TIME        | TIMESTAMP(6)   | Oldest sample timestamp                                                |
| LATEST_SAMPLE_ID          | BIGINT      | Latest sample ID                                                |
| LATEST_SAMPLE_TIME        | TIMESTAMP(6)   | Latest sample timestamp                                                |
| SAMPLE_COUNT              | BIGINT      | Total number of samples                                                |
| DROPPED_SAMPLE_COUNT      | BIGINT      | Number of dropped samples                                                |
| SAMPLED_BYTES             | BIGINT      | Total size of sampled data (unit: bytes)                                                |
| SAMPLER_ELAPSED_TIME      | BIGINT      | Total sampling time (unit: microseconds)                                                |
| DISK_FILTER_RATIO         | INTEGER     | Ratio of memory samples to disk samples                                                |
| LATEST_FLUSH_SAMPLE_ID    | BIGINT      | Latest flushed sample ID                                                 |
| AWR_FLUSH_BYTES           | BIGINT      | Total flushed data size (unit: bytes)                                                |
| AWR_FLUSH_ELAPSED_TIME    | BIGINT      | Total flush time (unit: microseconds)                                                |
| AWR_FLUSH_COUNT           | BIGINT      | Total number of flushes                                                |
| AWR_FLUSH_EMERGENCY_COUNT | BIGINT      | Number of emergency flushes                                                |
| CON_ID                    | INTEGER     | Current tenant ID (reserved field)                                                |