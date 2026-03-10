After configuring the logical standby database, the DBA should monitor and manage the daily usage of the logical standby database through the DBA_LOGSTDBY_EVENTS and V$DIAG_INCIDENT views.

## DBA\_LOGSTDBY\_EVENTS View

- This view records events related to SQL apply blocking due to resource errors (for example, insufficient tablespace, insufficient CURSOR POOL, etc.) on the logical standby database. If such events occur, DBA intervention is required for remediation.
- This view also records operations that are directly skipped during SQL apply on the logical standby database due to primary key update conflicts, which may lead to data inconsistency with the primary database.

## V$DIAG\_INCIDENT View
   
If there is insufficient STREAM POOL space, the SQL apply on the logical standby database will be interrupted, and the database will log and report the incident (in the V$DIAG_INCIDENT view). At this point, DBA intervention is needed to adjust the size of the STREAM POOL and restart the SQL apply.