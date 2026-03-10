## Restore the Entire CDB



Database recovery can be divided into:

- Complete database recovery: Extract, decrypt, and restore the backup set from the full database backup to the database directory, then replay all archive logs from the backup set to recover the database to a consistent state, enabling complete recovery of the database to the point in time corresponding to the backup set.

- Point-in-time recovery (PITR): Extract, decrypt, and restore the backup set from the full database backup to the database directory, then replay archive logs according to the target time point:

    - If the target time point is not later than the moment corresponding to the backup set, directly replay archive logs from the backup set until the target time point.
    
    - If the target time is later than the moment corresponding to the backup set, after replaying all archive logs from the full database backup set, automatically search for eligible archive log files (or their backup sets) for replay until reaching the target point or replaying all archive logs.





### Operation Instructions

- Only the SYS user can perform the entire database recovery.

- If the database backup set uses encrypted backups, the decryption keyword and password must be specified during recovery.

- In Standalone One-primary/Multi-standby Deployment, before performing recovery operations on the primary database (especially when only an isolated primary database remains in fault scenarios), the automatic failover functionality must first be disabled (set HA_ELECTION_LEADER_LEASE_ENABLED to FALSE) to prevent unintentional failover due to undetected standby database heartbeats, and restore the corresponding configuration after completion of recovery as needed.

- In YAC/Distributed Cluster Deployment, database recovery operations can only be performed on the primary instance (the instance whose INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE).

- If slice files for the LSC table were generated after the backup set, the following scenarios during point-in-time recovery of the entire database might lead to data inconsistencies in the LSC table after recovery:

  - If all original slice files for the database exist but an older point in time is used to restore, extra slice files may appear in the LSC table.

  - If the original slice files of the database are cleared and PITR is used, the LSC table may lose data stored in the slice files.

- Database environment preparation before recovery:

  - The CDB root and all PDBs must be in NOMOUNT stage.

  - Directories for data files, archive files, and bucket directories must be cleared.

  - If the backup set contains encrypted objects, the corresponding key wallet file must be opened before performing the recovery operation.

### Complete Restore of Entire Database

Directly restore database based on the backup set of the full database backup.

 

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE FROM TAG 'CDB_full_001'" \
-D /home/yashan/catalog
```



### Point-In-Time Restore of Entire Database

When recovering the entire database based on a point in time, the system first uses the backup set from the full database backup to restore data. If the target time point is not reached, the system automatically searches for eligible archive log files (or their backup sets) for replay until the target time point is reached or the latest log entry is replayed. If all archives are replayed but the target time point is still not reached, a message will be prompted: "the database cannot recover to the target time, and the current time of the database is {the actual nearest time point that can be recovered to}". In this case, ALTER DATABASE OPEN RESETLOGS must be executed to reset the redo log numbers when starting the database.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE FROM TAG 'CDB_full_001' UNTIL TIME '2023-11-13'" \
-D /home/yashan/catalog

# Specify SCN and database ID
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE DBID 12573483 UNTIL TIME '2025-10-13 18:55:00'" \
-D /home/yashan/catalog

# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE FROM TAG 'CDB_full_001' UNTIL SCN 499833430256541696" \
-D /home/yashan/catalog
```

## Restore the CDB Root



Database recovery can be divided into:

- Complete database recovery: Extract, decrypt, and restore the backup set from the full database backup to the database directory, then replay all archive logs from the backup set to recover the database to a consistent state, enabling complete recovery of the database to the point in time corresponding to the backup set.

- Point-in-time recovery (PITR): Extract, decrypt, and restore the backup set from the full database backup to the database directory, then replay archive logs according to the target time point:

    - If the target time point is not later than the moment corresponding to the backup set, directly replay archive logs from the backup set until the target time point.
    
    - If the target time is later than the moment corresponding to the backup set, after replaying all archive logs from the full database backup set, automatically search for eligible archive log files (or their backup sets) for replay until reaching the target point or replaying all archive logs.





### Operation Instructions

- Only the SYS user can perform the entire database recovery.

- If the database backup set uses encrypted backups, the decryption keyword and password must be specified during recovery.

- In Standalone One-primary/Multi-standby Deployment, before performing recovery operations on the primary database (especially when only an isolated primary database remains in fault scenarios), the automatic failover functionality must first be disabled (set HA_ELECTION_LEADER_LEASE_ENABLED to FALSE) to prevent unintentional failover due to undetected standby database heartbeats, and restore the corresponding configuration after completion of recovery as needed.

- In YAC/Distributed Cluster Deployment, database recovery operations can only be performed on the primary instance (the instance whose INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE).

- If slice files for the LSC table were generated after the backup set, the following scenarios during point-in-time recovery of the entire database might lead to data inconsistencies in the LSC table after recovery:

  - If all original slice files for the database exist but an older point in time is used to restore, extra slice files may appear in the LSC table.

  - If the original slice files of the database are cleared and PITR is used, the LSC table may lose data stored in the slice files.

- Database environment preparation before recovery:

  - The CDB root must be in NOMOUNT stage.

  - Directories for data files, archive files, and bucket directories must be cleared.

  - If the backup set contains encrypted objects, the corresponding key wallet file must be opened before performing the recovery operation.

### Complete Restore of Entire Database

Directly restore database based on the backup set of the full database backup.

 

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT FROM TAG 'root_full_backup'" \
-D /home/yashan/catalog
```



### Point-In-Time Restore of Entire Database

When recovering the entire database based on a point in time, the system first uses the backup set from the full database backup to restore data. If the target time point is not reached, the system automatically searches for eligible archive log files (or their backup sets) for replay until the target time point is reached or the latest log entry is replayed. If all archives are replayed but the target time point is still not reached, a message will be prompted: "the database cannot recover to the target time, and the current time of the database is {the actual nearest time point that can be recovered to}". In this case, ALTER DATABASE OPEN RESETLOGS must be executed to reset the redo log numbers when starting the database.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT FROM TAG 'root_full_backup' UNTIL TIME '2023-11-13'" \
-D /home/yashan/catalog

# Specify SCN and database ID
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT DBID 12573483 UNTIL TIME '2025-10-13 18:55:00'" \
-D /home/yashan/catalog

# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE DATABASE ROOT FROM TAG 'root_full_backup' UNTIL SCN 499833430256541696" \
-D /home/yashan/catalog
```

## Restore Specified PDB(s)



Database recovery can be divided into:

- Complete database recovery: Extract, decrypt, and restore the backup set from the full database backup to the database directory, then replay all archive logs from the backup set to recover the database to a consistent state, enabling complete recovery of the database to the point in time corresponding to the backup set.

- Point-in-time recovery (PITR): Extract, decrypt, and restore the backup set from the full database backup to the database directory, then replay archive logs according to the target time point:

    - If the target time point is not later than the moment corresponding to the backup set, directly replay archive logs from the backup set until the target time point.
    
    - If the target time is later than the moment corresponding to the backup set, after replaying all archive logs from the full database backup set, automatically search for eligible archive log files (or their backup sets) for replay until reaching the target point or replaying all archive logs.





### Operation Instructions

- Only the SYS user can perform the entire database recovery.

- If the database backup set uses encrypted backups, the decryption keyword and password must be specified during recovery.

- In Standalone One-primary/Multi-standby Deployment, before performing recovery operations on the primary database (especially when only an isolated primary database remains in fault scenarios), the automatic failover functionality must first be disabled (set HA_ELECTION_LEADER_LEASE_ENABLED to FALSE) to prevent unintentional failover due to undetected standby database heartbeats, and restore the corresponding configuration after completion of recovery as needed.

- In YAC/Distributed Cluster Deployment, database recovery operations can only be performed on the primary instance (the instance whose INSTANCE_ROLE field in the V$INSTANCE view is MASTER_ROLE).

- If slice files for the LSC table were generated after the backup set, the following scenarios during point-in-time recovery of the entire database might lead to data inconsistencies in the LSC table after recovery:

  - If all original slice files for the database exist but an older point in time is used to restore, extra slice files may appear in the LSC table.

  - If the original slice files of the database are cleared and PITR is used, the LSC table may lose data stored in the slice files.

- Database environment preparation before recovery:

  - The CDB root and specified PDB(s) must be in NOMOUNT stage.

  - Directories for data files, archive files, and bucket directories must be cleared.

  - If the backup set contains encrypted objects, the corresponding key wallet file must be opened before performing the recovery operation.

### Complete Restore of Entire Database

Directly restore database based on the backup set of the full database backup.

 

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 FROM TAG 'pdb1_full_backup'" \
-D /home/yashan/catalog
```



### Point-In-Time Restore of Entire Database

When recovering the entire database based on a point in time, the system first uses the backup set from the full database backup to restore data. If the target time point is not reached, the system automatically searches for eligible archive log files (or their backup sets) for replay until the target time point is reached or the latest log entry is replayed. If all archives are replayed but the target time point is still not reached, a message will be prompted: "the database cannot recover to the target time, and the current time of the database is {the actual nearest time point that can be recovered to}". In this case, ALTER DATABASE OPEN RESETLOGS must be executed to reset the redo log numbers when starting the database.



***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 FROM TAG 'pdb1_full_backup' UNTIL TIME '2023-11-13'" \
-D /home/yashan/catalog

# Specify SCN and database ID
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 DBID 29401764 UNTIL TIME '2025-10-13 18:55:00'" \
-D /home/yashan/catalog

# Specify SCN and database backup set
$ yasrman sys/********@192.168.1.2:1688 \
-c "RESTORE PLUGGABLE DATABASE pdb1 FROM TAG 'pdb1_full_backup' UNTIL SCN 499833430256541696" \
-D /home/yashan/catalog
```
