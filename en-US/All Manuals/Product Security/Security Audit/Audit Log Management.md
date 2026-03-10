## View Audit Logs

Users can view audit logs through the UNIFIED_AUDIT_TRAIL view, retrieving detailed information on security access, privilege operation records, etc., filtered by date, audit item type, object name, operating user, and more.

## Clean Up Audit Logs

For audit data that has been backed up or has met retention requirements, cleanup can be performed to avoid excessive disk space usage.

YashanDB provides the following cleanup strategies through the [DBMS_AUDIT_MGMT](../../开发手册/PL参考手册/内置高级包/DBMS_AUDIT_MGMT) advanced package for users to choose from:

- Set cleanup timestamp: Calling DBMS_AUDIT_MGMT.SET_LAST_ARCHIVE_TIMESTAMP can set the cleanup timestamp, allowing for more flexible specification of the audit data cleanup range.

- Manual cleanup: If necessary, users can call DBMS_AUDIT_MGMT.CLEAN_AUDIT_TRAIL to manually delete audit logs and free up space.

- Automatic cleanup: By calling DBMS_AUDIT_MGMT.CREATE_PURGE_JOB, users can establish an automatic cleanup task, set the start and stop flags for the scheduled task, and define the execution interval, allowing the system to regularly execute the audit data cleanup task.