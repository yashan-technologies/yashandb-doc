This view displays information about the YFS DiskGroup.

|Field |Type |Description |
|-------|------|------|
| ID        | INTEGER     | The ID of the diskgroup                                                    |
| NAME      | VARCHAR(32) | The name                                                                   |
| TYPE      | VARCHAR(32) | Type: <br>\* SYSTEM: System-specific disk group <br>\* USER: Ordinary user disk group |
| AU_SIZE        | INTEGER     | AU size    |
| BLOCK_SIZE| INTEGER     | The size of the file data block                                            |
| REDUNDANCY| VARCHAR(16) | The redundancy level of the diskgroup: <br>\* External: YFS does not provide mirroring; data protection is achieved by external storage systems <br>\* Normal: YFS provides two copies to protect data <br>\* High: YFS provides three copies to protect data |
| STATE     | VARCHAR(16) | The state of the diskgroup <br>\* BROKEN: Diskgroup failure <br>\* MOUNTED: Diskgroup operating normally <br>\* DISMOUNTED: State after a normally operating diskgroup has been dismounted |
| TOTAL_MB  | BIGINT      | Total space                                                                |
| FREE_MB   | BIGINT      | Available space                                                             |
| USABLE_FILE_MB | BIGINT | Available file size, the diskgroup's available space minus multiple copies |
