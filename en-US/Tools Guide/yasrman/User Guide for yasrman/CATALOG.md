CATALOG command is used to re-register backup set information, allowing backup sets not recorded in the current catalog to be added.

```ebnf
= CATALOG START WITH 'bakdir'.
```

Specify the backup set directory `bakdir` not registered in the current CATALOG, and the system will automatically scan for eligible backup sets within this directory. This operation does **not validate backup set integrity**, but scans the backup_profile metadata file of the backup sets and re-registers it into the current catalog.

The database must have read and write permissions on the `bakdir` directory.  

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "catalog start with '/data/bakdir'" -D /home/yashan/catalog

[YASRMAN CATALOG] found backupset: /data/bakdir/bak_root_full_client_001/backup_profile 
[YASRMAN CATALOG] register backupset: /data/bakdir/bak_root_full_client_001/cdb_backup.meta success 
[YASRMAN CATALOG] found backupset: /data/bakdir/cata_start_test backup_profile 
[YASRMAN CATALOG] register backupset: /data/bakdir/cata_start_test/cdb_backup.meta success 

yasrman sys/********@192.168.1.2:1688 -c "list backup" -D /home/yashan/catalog

Group: type DATABASE, tag: bak_root_full_client_001, format: /data/bakdir/bak_root_full_client_001, connect url: , nodeCount: 5, distribution: FALSE, isClient: TRUE, offset: 0
    backupset key: 672295430, base key: 0, pdbName: CDB$ROOT, uuid: C64430C0EAFE918D2D00C4248BA3DE16, container id: 0, backup path: /data/bakdir/bak_root_full_client_001/ROOT 
     instance: 1, archive range sequence: 2-2 
                  scn: 723031439944318976-723031502923321344 
    backupset key: 3629640063, base key: 0, backup path: /data/bakdir/bak_root_full_client_001/SEED 
     instance: 1, archive range sequence: 0-0 
                  scn: 0-0 
    backupset key: 3629640063, base key: 0, pdbName: PDB1, uuid: A4D730E5A564F65CF434A94D36F2A413, container id: 2, backup path: /data/bakdir/bak_root_full_client_001/PDB_PDB1 
     instance: 1, archive range sequence: 3-4 
                  scn: 723031438510563328-723031461925613568 
    backupset key: 3629640063, base key: 0, pdbName: PDB2, uuid: 88D0E7B9AA9891CF892A39F1BE732108, container id: 3, backup path: /data/bakdir/bak_root_full_client_001/PDB_PDB2 
     instance: 1, archive range sequence: 3-4 
                  scn: 723031438510563328-723031482805649408 
    backupset key: 3629640063, base key: 0, pdbName: PDB3, uuid: 3D2161B83AFCA9CAB2B6CB9963D16608, container id: 4, backup path: /data/bakdir/bak_root_full_client_001/PDB_PDB3 
     instance: 1, archive range sequence: 4-4 
                  scn: 723031501793935360-723031503358341120 

 
Group: type DATABASE, tag: cata_start_test, format: /data/bakdir/cata_start_test, connect url: , nodeCount: 5, distribution: FALSE, isClient: TRUE, offset: 45056
    backupset key: 1340642923, base key: 0, pdbName: CDB$ROOT, uuid: C64430C0EAFE918D2D00C4248BA3DE16, container id: 0, backup path: /data/bakdir/cata_start_test/ROOT 
     instance: 1, archive range sequence: 3-3 
                  scn: 723031502923321344-723031519058690048 
    backupset key: 2495971994, base key: 0, backup path: /data/bakdir/cata_start_test/SEED 
     instance: 1, archive range sequence: 0-0 
                  scn: 0-0 
    backupset key: 2495971994, base key: 0, pdbName: PDB1, uuid: A4D730E5A564F65CF434A94D36F2A413, container id: 2, backup path: /data/bakdir/cata_start_test/PDB_PDB1 
     instance: 1, archive range sequence: 5-5 
                  scn: 723031461925613568-723031516693581824 
    backupset key: 2495971994, base key: 0, pdbName: PDB2, uuid: 88D0E7B9AA9891CF892A39F1BE732108, container id: 3, backup path: /data/bakdir/cata_start_test/PDB_PDB2 
     instance: 1, archive range sequence: 5-5 
                  scn: 723031482805649408-723031514665512960 
    backupset key: 2495971994, base key: 0, pdbName: PDB3, uuid: 3D2161B83AFCA9CAB2B6CB9963D16608, container id: 4, backup path: /data/bakdir/cata_start_test/PDB_PDB3 
     instance: 1, archive range sequence: 5-5 
                  scn: 723031503358341120-723031513794142208 
```
