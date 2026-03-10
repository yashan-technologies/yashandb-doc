When YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), it provides dual backup and recovery capabilities at both CDB and PDB levels through [*yasrman*](../../../All Manuals/Tools Guide/yasrman/00yasrman). The hierarchical backup strategy supports both unified protection of the entire CDB and independent backup and recovery operations for individual PDBs, providing a flexible and efficient data security mechanism.

## CDB-Level Backup and Recovery

CDB-level backup and recovery capabilities are designed to provide global unified operations management capabilities, with core values mainly reflected in the following aspects:

- Holistic data protection: Provides one-stop full database protection capability, capable of backing up data, metadata, and system configuration information of the CDB Root and all PDBs simultaneously. This holistic protection ensures rapid recovery of the entire multi-tenant environment in case of disasters, significantly simplifying the complexity of backup management.

- Efficient operations management: Through CDB-level unified backup, administrators no longer need to perform backup operations PDB by PDB, significantly reducing the number of backup jobs and management costs. A single backup operation can complete the protection of the entire CDB, improving operations efficiency by dozens of times, particularly suitable for large-scale multi-tenant environments.

- Consistent data recovery: CDB-level backup ensures data consistency of all PDBs at the same point in time, avoiding timing issues caused by PDB-by-PDB backup. During recovery, it guarantees the data integrity of the entire multi-tenant environment, meeting strict business continuity requirements.

- Simplified disaster recovery: When major system failures or data center-level disasters occur, CDB-level recovery can quickly rebuild the entire multi-tenant environment, including all tenants' data and configurations. This "one-click" recovery capability significantly shortens disaster recovery time and reduces business interruption risks.

- Cost-optimized storage management: Through unified backup strategies, centralized storage and management of backup data can be achieved, fully utilizing storage resources and avoiding storage waste caused by duplicate backups. Meanwhile, it supports incremental backup and compression technologies, further optimizing storage costs.

CDB-level backup and recovery operations must be performed by connecting to the CDB root, with specific command keywords as follows:

| Target Object   | Backup Command  | Restore Command |
|-------------------------|--------------------------------------|-------------------------|
| Control files, data files, redo log files, and partial archive log files of the entire CDB (including the CDB root, PDB seed, and all PDBs) | BACKUP DATABASE | RESTORE DATABASE |
| Archive log files of the entire CDB (including the CDB root and all PDBs) | BACKUP ARCHIVELOG | RESTORE ARCHIVELOG |
| Control files, data files, redo log files, and partial archive log files of the CDB root | BACKUP DATABASE ROOT | RESTORE DATABASE ROOT |
| Archive log files of the CDB root  |   | BACKUP ARCHIVELOG ROOT | RESTORE ARCHIVELOG ROOT |
| Control files, data files, redo log files, and partial archive log files of the specified PDB(s) | BACKUP PLUGGABLE DATABASE | RESTORE PLUGGABLE DATABASE |
| Archive logs for the specified PDB(s) | BACKUP PLUGGABLE ARCHIVELOG | Directly connect to the target PDB to execute RESTORE ARCHIVELOG |

## PDB-Level Backup and Recovery

PDB-level backup and recovery are designed to provide tenant-level operations management capabilities, with values mainly reflected in the following aspects:

- Tenant-autonomous data protection: PDB-level backup empowers each tenant with independent data protection capabilities, allowing tenants to formulate personalized backup strategies according to their own business needs and SLA requirements. This autonomous model meets the differentiated demands of different tenants for data protection frequency, retention periods, and recovery point objectives (RPO).

- Precise resource control: Through PDB-level backup operations, backup resource usage can be precisely controlled, preventing frequent backups of large tenants from affecting the normal business of small tenants. Administrators can allocate corresponding backup windows and resource quotas for PDBs of different scales, achieving reasonable allocation and optimized utilization of resources.

- Efficient fault recovery: When a PDB experiences data corruption or logical errors, PDB-level backup supports independent recovery of that tenant without affecting the normal operation of other PDBs. This precise recovery capability significantly shortens fault recovery time and improves business continuity assurance levels.

- Flexible business support: PDB-level backup supports on-demand backup and recovery, particularly suitable for scenarios such as tenant migration, test environment setup, and data analysis. Tenants can perform independent backup operations on their data at any time, providing convenience for business innovation and data utilization.

- Cost-optimized backup strategy: Through PDB-level backup management, differentiated backup strategies can be formulated based on the importance and data change frequency of each tenant. Important tenants can adopt high-frequency backup, while general tenants can use lower-frequency backup strategies, optimizing backup costs while ensuring data security.

PDB-level backup and recovery mainly refers to operations performed by directly connecting to the target PDB. Its [operation methods](../../../All Manuals/Database Administration/Backup and Recovery/Backup and Recovery Using yasrman/00Backup and Recovery Using yasrman) are consistent with non-CDBs. The specific command keywords are as follows:

| Target Object | Backup Command  | Restore Command |
|-------------------------|-------------|-------------------------|
| Control files, data files, redo log files, and partial archive log files of the current PDB | BACKUP DATABASE | RESTORE DATABASE |
| Data files of the target tablespace(s) in the current PDB | BACKUP TABLESPACE | RECOVER TABLESPACE |
| Archive log files of the current PDB | BACKUP ARCHIVELOG | RESTORE ARCHIVELOG |
