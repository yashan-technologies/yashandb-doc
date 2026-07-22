DBMS_SYSTEM package provides a group of built-in stored procedures, used to write user-defined information into the database trace files or run logs, enabling users to perform fault diagnosis, ETL process monitoring, and custom error alerting, etc.

> **Note**:
>
> The user calling this stored procedure must have the `EXECUTE` privilege on `SYS.DBMS_SYSTEM`. Only the SYS user can directly use this stored procedure; other users must be granted the privilege by the SYS user, for example: `GRANT EXECUTE ON SYS.DBMS_SYSTEM TO <username>;`.


## KSDWRT

```plsql
DBMS_SYSTEM.KSDWRT (
    DEST   IN  INTEGER,
    TEXT   IN  VARCHAR2);
```

KSDWRT is a stored procedure used to write a user-defined string to the specified log destination.

| Parameter | Description |
| :--- | :--- |
| DEST | Log destination, the value can be 1, 2 or 3. When DEST is NULL or takes any other invalid value, it is processed as 1 by default.<br/>1: writes only to the current session trace file, with the file path being `$YASDB_DATA/log/trace/yashandb_.trc`.<br/>2: writes only to the run log, with the file path being `$YASDB_DATA/log/run/run.log`.<br/>3: writes to both the trace file and the run log. |
| TEXT |  The text content to be written, with a maximum length of 65534 bytes. When TEXT is NULL or an empty string, this stored procedure performs no operation and returns success directly.<br/>When the length of TEXT exceeds 1000 characters, the system automatically truncates it in chunks of 1000 characters, appends the `...` marker to each non-trailing chunk, and writes them in sequence |

The following principles apply when executing writes:

- The system formats the written content and concatenates information such as the timestamp, process ID, and session identifier. The complete format is `<timestamp> <pid> [INFO] SESSION#<sid>_<serial#> <content>`.
- When multiple sessions write to the same log destination concurrently, the system ensures data integrity and order through file locks, with no loss or out-of-order issues.

Examples

```plsql
-- DEST=1, write text to the current session trace file
BEGIN
  DBMS_SYSTEM.KSDWRT(1, 'Begin batch process: ' || TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS'));
END;
/

-- DEST=2, write text to the run log
BEGIN
  DBMS_SYSTEM.KSDWRT(2, 'Custom alert: order processing completed.');
END;
/

-- DEST=3, write text to both the trace file and the run log
BEGIN
  DBMS_SYSTEM.KSDWRT(3, 'Critical event triggered.');
END;
/
```
