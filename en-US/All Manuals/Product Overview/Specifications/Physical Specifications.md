Database
------

|Parameter Name |Parameter Type |Parameter Value |
| ------------------------ | --------------- | --------------- |
| Control File Count       | Minimum Value    | 2                                                       |
| Control File Count       | Default Value    | The default number of control files when creating a database is 3 |
| Control File Count       | Maximum Value    | 8                                                       |
| Data Block Size          | Maximum Value    | 32KB<br>Supports block sizes of 8KB, 16KB, 32KB       |
| Data Block Size          | Default Value    | 8KB                                                     |
| Instance Count           | Maximum Value    | 64                                                      |

Tablespace
-------

|Parameter Name |Parameter Type |Parameter Value |
| -------------------| --------------- | --------------- |
| Tablespace Count         | Maximum Value      | 2048            |
| Data File Count          | Maximum Value per Tablespace | 64        |
| Data File Count          | Maximum Value      | 16384           |
| Data File Size           | Minimum Value      | 1MB             |
| Data File Size           | Maximum Value      | 2TB             |
| Number of Databuckets per Tablespace | Maximum Value | 64      |
| Databucket Count         | Maximum Value      | 4096            |
| Databucket Count         | Default Value      | 256             |

Log Files
------

|Parameter Name |Parameter Type |Parameter Value |
| ------------------------ | ---------------- | :------------------------------------------------------------------------- |
| Log File Size            | Minimum Value   | 6MB <br>The minimum value is affected by DB_BLOCK_SIZE, MAX_SESSIONS, and REDO_BUFFER_SIZE parameters.<br>Refer to the formula: DB_BLOCK_SIZE * MAX_SESSIONS * 8 + REDO_BUFFER_SIZE / 2 |
| Log File Size            | Maximum Value   | 2047GB                                                                                            |
| Log Block Size           | Minimum Value   | 512Bytes                                                                                         |
| Log Block Size           | Maximum Value   | 32KB                                                                                             |
| Log Block Size           | Default Value   | 4KB                                                                                              |
| Log File Count           | Minimum Value per Instance | 3                                                                                |
| Log File Count           | Maximum Value per Instance | 256                                                                          |
| Archive File Count       | Maximum Value   | 1000000                                                                                          |

Primary/Standby High Availability
------

|**Parameter Name** |Parameter Type |Parameter Value |
| ---------------------- | ------------ |-----------------------------------------|
| Physical Standby Database  | Maximum Standby Database Count | Standalone Deployment: 32<br>YAC Deployment: 32 (Backup Cluster)<br>ISC (Integration Storage and Compute) Distributed Cluster Deployment: 4 (Backup Node) |
| Logical Standby Database    | Maximum Standby Database Count | Standalone Deployment: 32<br>YAC Deployment: No logical backup cluster<br>ISC Distributed Cluster Deployment: No logical standby database |
| Cascade Backup              | Maximum Cascade Count | Standalone Deployment: Unlimited<br>YAC Deployment: No cascade backup cluster<br>ISC Distributed Cluster Deployment: No cascade backup |
| Maximum Backup Set File Count | Maximum Value     | 17408                                           |
| Maximum Incremental Backup Count | Maximum Value   | 1000                                            |
| RTO                        | Maximum Value      | 30 seconds<br>Depends on leader election heartbeat and interval configuration |

## YAC Deployment

|Parameter Name |Parameter Type |Parameter Value |
| ---------------- | -------- | ------ |
| Number of Database Servers in the Same Cluster | Recommended Value  | 2 ~ 4            |
| Number of Database Instances Deployed on a Single Server in the Same Cluster | Maximum Value      | 1                |
| System Disk Size           | Minimum Value   | 1G<br/>When there is 1 system disk and AU Size is configured as 32M, the system disk size must be at least 2G |
| Number of System Disks     | Optional Values | 1, 3 or 5       |
| LUN Path Length            | Maximum Value   | 31B              |

## Yashan File System

|Parameter Name |Parameter Type |Parameter Value |
| ------------------------- | ------------ | -------------------------------------------------- |
| Number of Diskgroups        | Global Maximum Value  | 512                                              |
| Number of Failure Groups     | Global Maximum Value  | 2048                                             |
| Number of Failure Groups     | Maximum Value per Diskgroup | 16                                            |
| Number of Disks              | Global Maximum Value  | 65535                                            |
| Number of Disks              | Maximum Value per Diskgroup | 8192                                         |
| Number of Disks               | Maximum Value per Failure Group | 1024                                    |
| AU Size                     | Default Value         | 1M                                               |
| AU Size                     | Maximum Value         | 32M<br/>Supports sizes of 1M, 4M, 8M, 16M, 32M |
| Number of Connections        | Default Value         | 1024<br>Where internal connections are (1 + (number of instances - 1) * 2) |
| Number of Connections        | Maximum Value         | 65535<br>Where internal connections are (1 + (number of instances - 1) * 2) |
| Maximum Number of Files in a Single Diskgroup | Maximum Value      | 1 million                                   |
| Maximum File Size            | Maximum Value         | The maximum file size in YFS is related to AU size and redundancy, YFS supports creating files up to 180T |
| Maximum Disk Size            | Maximum Value         | 4G * AU size<br>When AU size is default, the maximum single disk size is 4PB |
| Minimum Disk Size            | Minimum Value         | 32 * AU size<br/>When AU size is default, the minimum single disk size is 32M |
| Maximum Disk Path Length     | Maximum Value         | 31                                                |
| Maximum Directory or File Name Length | Maximum Value  | 31                                             |
| Maximum Length for Diskgroup/Failuregroup/Disk Name | Maximum Value       | 31                                         |
| Maximum Length for Directory and File Absolute Path | Maximum Value        | 255                                          |

Distributed Cluster Deployment 
-------

|Parameter Name |Parameter Type |Parameter Value |
| ------ | ------------- |------------------------|
| Distributed Cluster Deployment   | Maximum Deployment Scale | 64CN         |

ISC Distributed Cluster Deployment 
-------

|Parameter Name |Parameter Type |Parameter Value |
| ----------- | ------------- |----------------|
| ISC Distributed Cluster Deployment        | Maximum Deployment Scale | 5MN 8CN 32*5DN |
