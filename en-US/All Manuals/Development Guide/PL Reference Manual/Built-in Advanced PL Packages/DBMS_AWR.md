The DBMS_AWR package provides a set of built-in procedures/functions for implementing performance report-related functionality.

## CREATE\_SNAPSHOT

```plsql
DBMS_AWR.CREATE_SNAPSHOT ();
```

The CREATE_SNAPSHOT procedure is a stored procedure with no parameters used to create a snapshot.

After successful execution of this procedure, the system will add a new snapshot record in the WRM$_SNAPSHOT table, which contains information such as snapshot ID, snapshot time, etc.

***Example***

```plsql
-- Create a snapshot
EXEC DBMS_AWR.CREATE_SNAPSHOT();
-- Continue to create another snapshot
EXEC DBMS_AWR.CREATE_SNAPSHOT();

-- Query the latest two snapshot records from the WRM$_SNAPSHOT table, including the database ID saving the snapshot information, the database ID of the source of the snapshot information, snapshot ID, instance identifier, etc.
SELECT dbid,src_dbid,snap_id,instance_number,group_id,group_node_id FROM sys.wrm$_snapshot ORDER BY snap_id DESC LIMIT 2;

       DBID    SRC_DBID     SNAP_ID INSTANCE_NUMBER    GROUP_ID GROUP_NODE_ID 
----------- ----------- ----------- --------------- ----------- ------------- 
 3230486619  3230486619           3               1           0             0
 3230486619  3230486619           2               1           0             0
```

## AWR\_REPORT

```plsql
DBMS_AWR.AWR_REPORT(
	l_dbid     IN NUMBER,            
	l_inst_num IN VARCHAR2,           
  	l_bid      IN NUMBER,            
  	l_eid      IN NUMBER,           
  	l_options  IN NUMBER   DEFAULT 0,
  	l_format   IN VARCHAR2 DEFAULT 'html');
```

The AWR_REPORT procedure is a stored procedure used to generate performance reports.

After successful execution of this procedure, the report content will be directly output as text, which the user can copy and paste into an HTML file for viewing and saving.

|Parameter |Description |
| :--------- | :----------------------------------------------------------- |
| l_dbid      | The database ID of l_inst_num, corresponding to SRC_DBID in the WRM$_SNAPSHOT table |
| l_inst_num  | Instance identifier<br/>* In Standalone Deployment, this parameter value is 1<br/>* In YAC/Distributed Cluster Deployment, this parameter value is taken from GV$INSTANCE.INSTANCE_NUMBER<br/>* In ISC Distributed Cluster Deployment, this parameter value is taken from the concatenation of GV$INSTANCE.GROUP_ID and GROUP_NODE_ID, input format like '3-1' |
| l_bid       | Starting snapshot ID                                            |
| l_eid       | Ending snapshot ID                                              |
| l_options   | Reserved field                                                 |
| l_format    | Reserved field                                                 |

***Example***

```plsql
-- Get two snap_id values from WRM$_SNAPSHOT table
-- Standalone/YAC/Distributed Cluster Deployment 
EXEC DBMS_AWR.AWR_REPORT(2621752453,1,168,169);

-- ISC Distributed Cluster Deployment 
EXEC DBMS_AWR.AWR_REPORT(2621752453,'3-1',168,169);
```

## CLEAN\_SNAPSHOT

```plsql
DBMS_AWR.CLEAN_SNAPSHOT ();
```

The CLEAN_SNAPSHOT procedure is a stored procedure with no parameters, used to check whether previously generated snapshots have exceeded the set retention time (default is 8 days); if exceeded, it will perform cleanup.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
EXEC DBMS_AWR.CLEAN_SNAPSHOT();
```

## DROP\_SNAPSHOT\_RANGE

```plsql
DBMS_AWR.DROP_SNAPSHOT_RANGE (
	low_snap_id      IN NUMBER,         
	high_snap_id     IN NUMBER,         
	dbid             IN NUMBER DEFAULT NULL);
```

The DROP_SNAPSHOT_RANGE procedure is a stored procedure that deletes the snapshots in the range specified by given parameters.

|Parameter |Description |
| :----------- |:---------------------------------------------------------|
| low_snap_id   | Starting snapshot ID                                       |
| high_snap_id  | Ending snapshot ID                                        |
| dbid          | The database ID saving the snapshot information, corresponding to DBID in the WRM$_SNAPSHOT table; can be omitted, and the current database ID will be automatically filled |

***Example***

```plsql
EXEC DBMS_AWR.DROP_SNAPSHOT_RANGE(35, 40);
```

## MODIFY\_SNAPSHOT\_SETTINGS

```plsql
DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(
	retention       IN NUMBER   DEFAULT NULL,        
	interval        IN NUMBER   DEFAULT NULL,
    dbid            IN NUMBER   DEFAULT NULL,
    topnsql         IN VARCHAR2 DEFAULT NULL);
```

The MODIFY_SNAPSHOT_SETTINGS procedure is a stored procedure that controls the generating interval and retention time of snapshots by modifying relevant properties of the snapshot.

|Parameter |Description |
| :-------- |:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| retention   | Snapshot retention time, in minutes. For example, to retain for one day, input: 24*60 (minimum value is one day, maximum is 100 years); entering 0 means to keep the snapshot permanently                                        |
| interval    | Snapshot generation interval, in minutes. For example, to generate a snapshot every 20 minutes, input: 20 (minimum value is 10 minutes, maximum is 100 years); entering 0 disables both manual and automatic snapshot collection                     |
| dbid        | Database ID saving the snapshot information; can be omitted, and the current database ID will be automatically filled                                                                                                          |
| topnsql     | Specifies the number of Top SQL refreshed for each SQL standard (ELAPSED_TIME, CPU_TIME, PARSE_CALLS, SHARABLE_MEM) when generating snapshots; the default value is NULL, indicating to keep the current setting. It allows users to specify as follows:<br>* Specific value N: i.e., TOP N, where the valid range is [30,50000].<br>* DEFAULT: to restore the system to default behavior, which is TOP 30.<br>* MAXIMUM: to retrieve all SQL in the view. |

> **Note**: 
>
> The default snapshot retention time for the system is 8 days, and the default snapshot generation interval is 1 hour.

***Example***

```plsql
-- Set snapshot retention time to 3 days and snapshot generation interval time to 2 hours
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(3*24*60,2*60);

-- Specify the number of top SQL to be 60
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(topnsql=>60);

-- Specify the number of top SQL to the default value
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(topnsql=>'DEFAULT');

-- Specify the number of top SQL to MAXIMUM
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(topnsql=>'MAXIMUM');
```
