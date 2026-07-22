YashanDB provides multiple methods to capture diagnostic data, assisting with fault tracking and analysis.

## Manual Capture of Diagnostic Data

YashanDB offers the dump statement, allowing users to obtain internal structural information as needed.

> **Note**: 
>
> If you wish to obtain a detailed analysis of the relevant internal structural data during fault tracking, please consult our technical support.

### Dump Statement

The dump is implemented based on the SQL statement, used to dump internal data at the time of executing operations (multiple dumps of similar information can track changes). For detailed syntax description, please refer to the dump_clause section of [ALTER SYSTEM](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM).

Dump operations involve disk writing, which will have some impact on IO during runtime, so please arrange the operation time reasonably.

YashanDB allows the following internal information to be dumped into trace files:

- **private redo in memory**: Private log data in memory

    ```sql
    ALTER SYSTEM DUMP PRIVATE LOG;
    ```

- **logfile blocks**: Redo log data on disk

    ```sql
    ALTER SYSTEM DUMP LOGFILE 'redo1'; 
    ```

- **datafile blocks**: Data from data files on disk

    ```sql
    -- dump the entire data file 
    ALTER SYSTEM DUMP DATAFILE 6;

    -- dump a single page
    ALTER SYSTEM DUMP DATAFILE 6 BLOCK 0;

    -- dump a batch of pages
    ALTER SYSTEM DUMP DATAFILE 6 MINBLOCK 128 MAXBLOCK 137;
    ```

- **stack of this session**: Stack data of a specific session

    ```sql
    ALTER SYSTEM DUMP SESSION 20 BACKTRACE;
    ```

- Deadlock information (automatically triggers dump)

    Deadlock information cannot be manually dumped using SQL statements and can only be triggered automatically when a deadlock is detected. When the database background thread detects a deadlock, it will select one session from the deadlock cycle to return the corresponding error, while automatically dumping the relevant [deadlock information](#deadlock) to the trace file corresponding to that session.

### Dump Results

After the first execution of the dump operation, a trace file corresponding to the session ID (sid) will be generated, with the default filename format {dbname}_yas_{sid}.trc. Subsequent dump operations will only append new data to the existing file corresponding to the same session ID.

> **Note**: 
>
> Since session IDs can be reused, when a new session with the same ID performs a dump for the first time and the trace file corresponding to that ID already exists, no new file will be created; data will continue to be appended.

Each dump operation corresponds to a section in the corresponding file, with each section consisting of a title and data:

- Title: Contains information such as dump time, dump type, and dump data size.

- Data: Internal structure data of the system obtained based on different dump types.

<span id="blackbox" name="blackbox"></span>

## Automatic Capture of Diagnostic Data

YashanDB provides an automatic capture mechanism for diagnostic data, which collects process running stack information before the database process experiences a fault crash and dumps the diagnostic data into the automatic diagnostic repository's blackbox directory. This mechanism is similar to a flight recorder (i.e., an aircraft "black box"), hence it is also referred to as the "black box."

> **Note**: 
>
> When "black box" diagnostic data is collected, please package the diagnostic data and contact our technical support in a timely manner.

<span id="deadlock" name="deadlock"></span>

## Deadlock Detection Diagnostic Data

YashanDB provides an automatic deadlock detection mechanism, where the background thread SMON regularly checks for deadlocks. If a deadlock is detected, it will automatically dump the deadlock cycle information into the corresponding trace file.

The deadlock cycle information includes resource identifiers, resource owners, resource requesters, deadlock session IDs, session users, the SQL statements executed by the sessions, and the specific values of SQL binding parameters.

Resource identifiers are categorized as:

- `TM-XXXX-XXXX`: Table lock resource, where XXXX-XXXX is the object ID (i.e., table object ID).

- `TX-XXXX-XXXX`: Transaction lock and xslot lock resource, where XXXX-XXXX is xext (first two hexadecimal digits) + xnode (middle two hexadecimal digits) + xsn (last four hexadecimal digits).

Lock types are categorized as:

- X: Represents an exclusive lock.

- S: Represents a shared lock.

```shell
Deadlock graph:
                                         -----------Blocker(s)------------ -----------Waiter(s)-------------
 Resource Name                           instId session holds waits serial instId session holds waits serial
 TX-0031344A-00000001                         0      25     X            3      0      22           X      4
 TX-0030344A-00000001                         0      22     X            4      0      25           X      3
 ----- Information for waiting sessions -----
 instId 0 Session 22:
   sid: 22 serial: 4 user: SYS
   current SQL:
   update t1 set a = 3 where b = :B0
   bind params:
   4
 instId 0 Session 25:
   sid: 25 serial: 3 user: SYS
   current SQL:
   update t1 set a = 1 where b = :B0
   bind params:
   2
 ----- End of information for waiting sessions -----
```
