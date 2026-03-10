The DBMS_LOGSTDBY package is primarily used to manage logic standby databases.



Logical replication feature applies to both Standalone Deployment and YAC Deployment, but in YAC Deployment, it applies only to rolling upgrade.


## BUILD

```plsql
DBMS_LOGSTDBY.BUILD ();
```

Using this stored procedure on the primary database will record logical information in the redo log and automatically enable the primary database's [database-level supplemental logging](../../SQL Reference Manual/SQL Statements/ALTER DATABASE.html#supplementallogclauses), with the supplemental logging mode set to PRIMARY KEY, and the effective table type being HEAP.

Before converting a physical standby database into a logical standby database, this procedure must be executed on the primary database.

> **Note**: 
>
> For each logical standby database created, DBMS_LOGSTDBY.BUILD should be run once.

***Example*** for Standalone Deployment and YAC Deployment

```plsql
EXECUTE DBMS_LOGSTDBY.BUILD;
```

<span id="apply_set" name="apply_set" class="yaslink"></span>

## APPLY\_SET

The APPLY_SET function can configure and manage SQL apply parameter values in a logical standby database, where the APPLY_SERVERS parameter cannot be changed during SQL apply.

```plsql
DBMS_LOGSTDBY.APPLY_SET (
     inname             IN VARCHAR,
     value              IN VARCHAR);
```

|Parameter |Description |
|-------|------|
| APPLY_SERVERS     | Controls the number of threads used for apply, default 16, minimum 1, maximum 1024. |
| MAX_EVENTS_RECORDED | The YashanDB will record all events encountered during SQL apply, presenting the most recent N events in the DBA_LOGSTDBY_EVENTS view (N is controlled by the MAX_EVENTS_RECORDED parameter value). The default value is 10000, minimum value is 1, maximum value is 1000000. |

***Example*** for Standalone Deployment and YAC Deployment

```plsql
-- Set the number of threads for SQL apply to 16
EXECUTE DBMS_LOGSTDBY.APPLY_SET('APPLY_SERVERS', '16');
```
