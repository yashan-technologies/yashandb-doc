This view displays YFS disk (Disk) information.

|Field |Type |Description |
|------------------|--------------|---------------------------------------------------------------------------|
| GROUP_ID         | NUMBER       | Group ID                                                                    |
| GROUP_NODE_ID    | NUMBER       | Node ID within the group                                                     |
| INST_ID          | NUMBER       | Instance ID                                                                 |
| ID               | INTEGER      | The ID of the disk within its belonging Diskgroup                            |
| GLOBAL_ID        | INTEGER      | The global ID of the disk                                                   |
| NAME             | VARCHAR(32)  | Name                                                                       |
| GROUP_NUMBER     | INTEGER      | The ID of the belonging Diskgroup                                           |
| FAILGROUP_ID     | INTEGER      | The ID of the belonging Failgroup                                           |
| FAILGROUP_LABEL  | VARCHAR(32)  | The name of the belonging Failgroup                                         |
| MOUNT_STATUS     | VARCHAR(16)  | Status<br>\* NORMAL: The belonging Diskgroup MOUNTED<br>\* CLOSED: The belonging Diskgroup DISMOUNTED |
| REDUNDANCY       | VARCHAR(16)  | Redundancy <br>\* EXTERNAL<br>\* NORMAL<br>\* HIGH                         |
| PARTNERS         | VARCHAR(161) | List of global IDs of partner disks, separated by spaces                   |
| TOTAL_MB         | BIGINT       | Total space size                                                           |
| FREE_MB          | BIGINT       | Free space size                                                            |
| PATH             | VARCHAR(32)  | Block device path                                                           |
| READS            | BIGINT       | Number of read requests                                                     |
| WRITES           | BIGINT       | Number of write requests                                                    |
| BYTES_READ       | BIGINT       | Number of bytes read                                                        |
| BYTES_WRITTEN    | BIGINT       | Number of bytes written                                                     |
| READ_TIME        | BIGINT       | Time spent reading                                                         |
| WRITE_TIME       | BIGINT       | Time spent writing                                                         |