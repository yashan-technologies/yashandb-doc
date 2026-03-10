*yasbak* is a tool provided by YashanDB for third-party integration with backup and recovery functionality. It supports client-side deployment and offers a non-intrusive backup and recovery integration solution.

- yasbak is essentially a secondary encapsulation of *yasrman*, saving some essential parameters required during the execution of *yasrman*, thereby reducing the number of input parameters for users.
- For detailed information on backup and recovery syntax, please refer to the tool manual [yasrman](../yasrman/00yasrman).

## Prerequisites

- The integrated YashanDB must be deployed using [command line method](../../Installation and Upgrade/Installation and Deployment/YashanDB Installation via CLI/00YashanDB Installation via CLI).
- It is recommended to execute *yasbak* with a non-root user.
- Disable all server firewalls or ensure normal network communication between all servers.

## Notes

1. When using this method to perform database backups, the following requirements must be met:

- The database cluster must be in OPEN status, with all nodes online (if some standby databases are abnormal, as long as it does not affect the corresponding primary database's transaction execution, it will not affect the backup).
- All nodes must have archiving mode enabled.
- The catalog file corresponding to the yasrman tool must have been created.
- The backup command can only be executed by the SYS superuser or users with SYSDBA or SYSBACKUP privileges. The recovery command can only be executed by the SYS user.
- Backup operations are not allowed to be executed concurrently with additions, deletions, or resize operations of tablespaces or data files (such as redo files); any such operation must be completed before executing the backup.

2. When using this method to perform database recovery, the following requirements must be met:

- All database nodes must be in NOMOUNT status.
- All residual files on the nodes must be cleaned up.
- The deployment status of the database cluster must remain consistent with that during the backup, such as the number of nodes, listening addresses, and paths, etc., must not have changed.

3. When using this method to recover archive log files, the following requirements must be met:

- The database must be in MOUNT status after executing the RESTORE command but before executing RECOVER.

4. When performing incremental backup set recovery, the following points must be noted:

- If the zero-level incremental backup is assigned the tag incr_0, and the ordinary first-level incremental backups are assigned tags incr_1_1, incr_1_2... (backups initiated in order):
   - When restoring the first-level incremental backup with the tag incr_1_2, it must ensure that the backup file storage directory for the zero-level incremental backup with tag incr_0 and the ordinary first-level incremental backup with tag incr_1_1 exists and is complete; otherwise, it will trigger YAS-00313 error.
- If the zero-level incremental backup is assigned the tag incr_0, and the cumulative first-level incremental backups are assigned tags incr_1_1, incr_1_2... (backups initiated in order):
   - When restoring the first-level incremental backup with the tag incr_1_2, it must ensure that the backup file storage directory for the zero-level incremental backup with tag incr_0 exists and is complete; otherwise, it will trigger YAS-00313 error.
- If the zero-level incremental backup is assigned the tag incr_0, cumulative first-level incremental backups are assigned tags incr_1_1, ordinary first-level incremental backup is assigned tag incr_1_2, cumulative first-level incremental backups are assigned tags incr_1_3, ordinary first-level incremental backups are assigned tag incr_1_4... (backups initiated in order):
   - When restoring the first-level incremental backup with the tag incr_1_4, it must ensure that the backup file storage directories for the incremental backups with tags incr_0 and incr_1_3 exist and are complete; otherwise, it will trigger YAS-00313 error.
   - When restoring the first-level incremental backup with the tag incr_1_3, it must ensure that the backup file storage directory for the zero-level incremental backup with tag incr_0 exists and is complete; otherwise, it will trigger YAS-00313 error.
- In HA mode, since each backup set of a replication group is only stored on the primary node, incremental backup sets and their dependency backup sets within the same replication group must reside on the same node.