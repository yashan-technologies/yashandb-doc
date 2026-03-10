This view displays YFS disk group (Diskgroup) information.

|Field |Type |Description |
|----------------|-------------|------------------------------------------------|
| GROUP_ID       | NUMBER      | Group ID                                        |
| GROUP_NODE_ID  | NUMBER      | Node ID within the group                        |
| INST_ID        | NUMBER      | Instance ID                                     |
| ID             | INTEGER     | ID of the Diskgroup                             |
| NAME           | VARCHAR(32) | Name                                            |
| TYPE           | VARCHAR(32) | Type:<br>\* SYSTEM: System dedicated disk group<br>\* USER: Ordinary user disk group|
| AU_SIZE        | INTEGER     | Allocate Unit Size, minimum space unit size    |
| BLOCK_SIZE     | INTEGER     | File data block size                            |
| REDUNDANCY     | VARCHAR(16) | Redundancy level of the disk group:<br>\* External: YFS does not provide mirroring, data protection is implemented by external storage systems<br>\* Normal: YFS provides two copies for data protection<br>\* High: YFS provides three copies to protect data|
| STATE          | VARCHAR(16) | State of the disk group:<br>\* BROKEN: Disk group failure<br>\* MOUNTED: Disk group state is normal<br>\* DISMOUNTED: State after a normally mounted disk group is unmounted|
| TOTAL_MB       | BIGINT      | Total space                                     |
| FREE_MB        | BIGINT      | Available space                                 |
| USABLE_FILE_MB | BIGINT      | Usable file size, available space of the Diskgroup minus multiple copies|