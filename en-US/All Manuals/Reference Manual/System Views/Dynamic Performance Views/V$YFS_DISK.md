This view displays YFS disk information.

|Field |Type |Description |
|-------|------|------|
| ID             | INTEGER  | The ID of the disk within the associated Diskgroup      |
| GLOBAL_ID      | INTEGER  | The global ID of the disk                               |
| NAME           | VARCHAR(32) | Name                                                  |
| GROUP_NUMBER    | INTEGER  | The ID of the associated Diskgroup                      |
| FAILGROUP_ID   | INTEGER  | The ID of the associated Failgroup                      |
| FAILGROUP_LABEL| VARCHAR(32) | The name of the associated Failgroup                  |
| MOUNT_STATUS    | VARCHAR(16) | Status: NORMAL (Diskgroup MOUNTED), CLOSED (Diskgroup DISMOUNTED) |
| REDUNDANCY     | VARCHAR(16) | Redundancy: EXTERNAL, NORMAL, HIGH                    |
| PARTNERS       | VARCHAR(161) | List of global IDs of partner disks, separated by spaces |
| TOTAL_MB       | BIGINT   | Total space size                                       |
| FREE_MB        | BIGINT   | Free space size                                        |
| PATH           | VARCHAR(32) | Block device path                                     |
| READS          | BIGINT   | Number of read requests                                 |
| WRITES         | BIGINT   | Number of write requests                                |
| BYTES_READ     | BIGINT   | Number of bytes read                                    |
| BYTES_WRITTEN  | BIGINT   | Number of bytes written                                 |
| READ_TIME      | BIGINT   | Time taken to read                                      |
| WRITE_TIME     | BIGINT   | Time taken to write                                     |