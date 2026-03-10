YashanDB defines a series of tracing events, allowing users to selectively trace different events to understand the SQL execution process, the internal state of various YashanDB components at different stages, and other information to assist with performance observation and tuning.

## Tracing Events

Each tracing event has a unique ID corresponding to different tracing targets. Events are specified using the event ID for starting or stopping tracing.

The supported events in YashanDB are shown in the table below:

|Tracing Event ID |Trace Level |Trace Target |
|--------------------|--------------------|------------------|
| 10046                   | **1** (default) Parsing, execution, extraction counts, time, CPU time, physical reads, logical reads, row counts, commits and rollbacks, execution plan rows, time, consistency reads, physical reads, physical writes, etc. <br/>**4**<br/>level1 + bind parameters <br/>**8**<br/>level1 + wait events <br/>**12**<br/> level1 + level4 + level8 | The 10046 event is used to trace performance information during SQL statement execution, with different information written based on the enabled level. |
| 10053                   | **1** (default)<br/>**2**<br/>Level 1 outputs more optimizer parameter information than level 2 | Traces the execution state of the CBO optimizer during the parsing stage, primarily including self-top-down operator selection process information, candidate operator cost information, and statistics for calculating operator costs. |

## Enabling Tracing

According to the effective scope of tracing, the operations to enable tracing can be categorized as follows:

- Session-level tracing: Executing the [ALTER SESSION](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER SESSION) statement allows tracing for the current session.
- Global tracing: Executing the [ALTER SYSTEM](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER SYSTEM) statement enables tracing globally. Global tracing may impact system performance and is **not recommended** unless necessary.

When enabling tracing, the following attribute information can be specified as needed:

- EVENT_ID: Tracing event ID, a required parameter to specify the specific tracing event based on the ID value.
- LEVEL LEVEL_NUM: Trace level, an optional parameter; each tracing event supports different trace levels, and the output information varies at different levels.

TRACE NAME CONTEXT, FOREVER: Only for syntax compatibility, with no actual significance.

```sql
-- Trace the 10053 event at the default level
ALTER SESSION SET EVENTS = '10053';
ALTER SESSION SET EVENTS = '10053 TRACE NAME CONTEXT';
ALTER SESSION SET EVENTS = '10053 TRACE NAME CONTEXT FOREVER';

-- Trace the 10053 event at level 2
ALTER SESSION SET EVENTS = '10053 TRACE NAME CONTEXT FOREVER, LEVEL 2';
    
-- Trace the 10046 event at level 12
ALTER SESSION SET events='10046 trace name context forever,level 12';
    
-- Trace the 10053 event at the default level, and the 10046 event
ALTER SESSION SET events='10046:10053';
```

> **Note**: 
>
> Since YashanDB has completed parsing the historical execution SQL buffer, enabling the 10053 trace may not capture the trace logs due to hitting the SQL buffer. Therefore, it is recommended to make a slight modification to the business SQL and then re-execute it to perform a hard parse and record the trace logs. When rewriting the SQL, you can add meaningless strings within the SQL statement, for example, the original SQL statement `select c1 from t1` can be rewritten as `select /*abc*/ c1 from t1`.

## Trace Results

The trace results are carried in trace files, differentiated by time and session ID (sid), with the default naming format being {dbname}_{yyyymmdd}_{sid}.trc.

### File Name Configuration

YashanDB supports adding custom identifiers to the trace file name, specified by the TRACE_FILE_IDENTIFIER parameter.

```sql
ALTER SESSION SET TRACE_FILE_IDENTIFIER = 'YashanDB_Trace';
-- After setting, the generated trace file's name will be {dbname}_{yyyymmdd}_{sid}_YashanDB_Trace.trc
```

### File Content

#### 10053

The output information for tracing the 10053 event includes trace file path information, database version information, session ID and start time, traced SQL statement, optimizer parameter information (level 1), operator trace information, and execution plan information. Sample output is shown below:

```text
Trace file $YASDB_DATA/diag/trace/yashandb_20250331_22.trc
YashanDB Server Enterprise Edition {version} x86_64
Session ID: 22  (2025-03-31 16:04:13.591)

**************** Trace Start [Event: 10053  Level: 1] ****************
Current SQL statement:
select 1+2 from dual

******************** CBO Parameters ******************

BLOOM_FILTER_FACTOR                      = 0.30
DML_PARALLEL                             = FALSE
FILTER_EXTEND_MODE                       = NOTNULL
FILTER_OR_TO_UNION                       = TRUE
MAX_RECURSION_DEPTH                      = 1000
OPT_FILTER_THRESHOLD                     = 0.30
OPTIMIZER_DYNAMIC_SAMPLING               = 0
QUERY_REWRITE_ENABLED                    = FALSE
SQL_MAP                                  = FALSE
STATISTICS_LEVEL                         = TYPICAL
SUBQUERY_NDV_FACTOR                      = 0.10
USE_STORED_OUTLINES                      = FALSE
_COLUMNAR_ENABLE_HASH_GROUP_DISTINCT     = TRUE
_COLUMNAR_ENABLE_FILTER_INVALID          = FALSE
_RWRT_OPT                                = 255
_ENABLE_EXPLAIN_STAGE                    = TRUE
_ENABLE_EXPLAIN_STATS                    = TRUE
_OPTIMIZE_EXTEND_FILTER                  = TRUE
_SPAREINDEX_OPT                          = ON
_TAC_FILTER_PUSH_THRESHOLD               = 5
_OPTMZ_MINMAX_OPT                        = SINGLE
_OPTMZ_EARLY_GROUP_OPT                   = ON
_OPTMZ_EXEC_ENGINE                       = ROW
_OPTMZ_ENABLE_DSTB_AC                    = TRUE
_B_TREE_BITMAP_PLANS                     = TRUE
_OPTMZ_ENABLE_DUP_TABLE_PARALLEL         = TRUE
_HASH_JOIN_ALGORITHM                     = PARTITION

*************** CBO Optimize Trace Start *************

Group 0
  Logical : Select [ 1 ] 
  Physical 0 :  Select [ 1 ] 

Group 1
  Logical : Project [ 2 ] 
  Physical 0 :  Project [ 2 ] 

Group 2
  Logical : Scan   Table Name: X$DUAL
  Physical 0 :  TableFullScan   Table Name: X$DUAL

  Physical 0: TableFullScan
  Stats:  rows=1  rowSize=0  keyDistinct=1  columns=1  selectivity=1.000000
  Cost:   self=8.004000  total=8.004000  fetch=0.000088 

  Group 2 Best :
  Logical : Scan   Table Name: X$DUAL
  Best Physical : TableFullScan   Table Name: X$DUAL
  Cost:   self=8.004000  total=8.004000  fetch=0.000088 

  Physical 0: Project
  Stats:  rows=1  rowSize=0  keyDistinct=1  columns=1  selectivity=1.000000
  Cost:   self=0.000000  total=8.004000  fetch=0.000088 

  Group 1 Best :
  Logical : Project [ 2 ] 
  Best Physical : Project [ 2 ] 
  Cost:   self=0.000000  total=8.004000  fetch=0.000088 

  Physical 0: Select
  Stats:  rows=1  rowSize=8  keyDistinct=1  columns=1  selectivity=1.000000
  Cost:   self=0.000000  total=8.004000  fetch=0.000088 

  Group 0 Best :
  Logical : Select [ 1 ] 
  Best Physical : Select [ 1 ] 
  Cost:   self=0.000000  total=8.004000  fetch=0.000088 

*************** Trace Bottom Up **********************

Group 2
  Logical : Scan   Table Name: X$DUAL
  Physical 0 :  TableFullScan   Table Name: X$DUAL

  Physical 0: TableFullScan
  Stats:  rows=1  rowSize=0  keyDistinct=1  columns=1  selectivity=1.000000
  Cost:   self=8.004000  total=8.004000  fetch=0.000088 

  Group 2 Best :
  Logical : Scan   Table Name: X$DUAL
  Best Physical : TableFullScan   Table Name: X$DUAL
  Cost:   self=8.004000  total=8.004000  fetch=0.000088 

Group 1
  Logical : Project [ 2 ] 
  Physical 0 :  Project [ 2 ] 

  Group 1 Best :
  Logical : Project [ 2 ] 
  Best Physical : Project [ 2 ] 
  Cost:   self=0.000000  total=8.004000  fetch=0.000088 

Group 0
  Logical : Select [ 1 ] 
  Physical 0 :  Select [ 1 ] 

  Group 0 Best :
  Logical : Select [ 1 ] 
  Best Physical : Select [ 1 ] 
  Cost:   self=0.000000  total=8.004000  fetch=0.000088 

***************** CBO Optimize Trace End *************

******************************************************
*                   Executor Plan                    *
******************************************************
SQL hash value: 2231489676
Optimizer: ADOPT_C
 
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  TABLE ACCESS FULL             | X$DUAL               | SYS        |         1|        8( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
***************** Trace End [Event: 10053  Level: 1] *****************
```

#### 10046

When tracing the 10046 event at level 12, the output information includes the example shown below:

```text
Trace file $YASDB_DATA/diag/trace/yashandb_20250331_22.trcyashandb_20250417_27_trace_event_10046.trc
YashanDB Server Enterprise Edition Debug 23.2.12.0 x86_64 67d26ef
Session ID: 27  (2025-04-17 11:43:29.302)

=====================
PARSING IN CURSOR #140146796809528 len=61 dep=0 uid=0 yct=7 lid=0 tim=1744861410698210 hv=143826514 ad='7f76780a5648' sqlid='1wsv3qmr968yu'
BEGIN DBMS_OUTPUT.GET_LINES(?,?); DBMS_OUTPUT.PUT(NULL); END;
END OF STMT
PARSE #140146796809528 c=22,e=22,p=0,cr=0,cu=0,mis=0,r=0,dep=0,og=1,plh=0,tim=1744861410698214
BINDS #140146796809528:

  Bind#0
    ydbdty=37 mxl=0 scl=0 pre=0 avl=0 toid=0
    value="NULL"
  Bind#1
    ydbdty=4 mxl=4 scl=0 pre=0 avl=4 toid=0
    value="2147483647"
WAIT #140146852137560: nam='log file sync' ela= 1 tim=1744861410698717
WAIT #140146852153968: nam='log file sync' ela= 0 tim=1744861410698851
EXEC #140146796809528 c=1190,e=1190,p=0,cr=0,cu=0,mis=0,r=0,dep=0,og=1,plh=0,tim=1744861410699410
WAIT #140146796809528: nam='db file scattered read' ela= 12 tim=1744861410700963
WAIT #140146796809528: nam='log file parallel write' ela= 66 tim=1744861410701784
WAIT #140146796809528: nam='redo remote sync complete' ela= 516 tim=1744861410702303
WAIT #140146796809528: nam='log file sync' ela= 593 tim=1744861410702304
WAIT #140146796809528: nam='db file scattered read' ela= 8 tim=1744861410702836
WAIT #140146796809528: nam='log file parallel write' ela= 69 tim=1744861410703035
WAIT #140146796809528: nam='redo remote sync complete' ela= 481 tim=1744861410703519
WAIT #140146796809528: nam='log file sync' ela= 559 tim=1744861410703520
CLOSE #140146796809528 type=0,tim=1744861410703989
WAIT #140146801009976: nam='SQL**** message to client' ela= 25 tim=1744861410704136
WAIT #140146801009976: nam='db file scattered read' ela= 14 tim=1744861410704207
WAIT #140146801009976: nam='log file parallel write' ela= 65 tim=1744861410705127
WAIT #140146801009976: nam='redo remote sync complete' ela= 528 tim=1744861410705657
WAIT #140146801009976: nam='log file sync' ela= 604 tim=1744861410705658
WAIT #140146801009976: nam='db file scattered read' ela= 9 tim=1744861410706141
WAIT #140146801009976: nam='log file parallel write' ela= 68 tim=1744861410706386
WAIT #140146801009976: nam='redo remote sync complete' ela= 754 tim=1744861410707144
WAIT #140146801009976: nam='log file sync' ela= 834 tim=1744861410707148
WAIT #140146801009976: nam='db file scattered read' ela= 11 tim=1744861410708790
WAIT #140146801009976: nam='log file parallel write' ela= 68 tim=1744861410709681
WAIT #140146801009976: nam='redo remote sync complete' ela= 532 tim=1744861410710217
WAIT #140146801009976: nam='log file sync' ela= 612 tim=1744861410710219
WAIT #140146801009976: nam='db file scattered read' ela= 11 tim=1744861410710890
WAIT #140146801009976: nam='log file parallel write' ela= 68 tim=1744861410711091
WAIT #140146801009976: nam='redo remote sync complete' ela= 503 tim=1744861410711597
WAIT #140146801009976: nam='log file sync' ela= 582 tim=1744861410711599
STAT #140146801009976 id=1 cnt=0 pid=0 pos=1 obj=0 op='SELECT (cr=0 cu=0 pr=0 pw=0 time=130 us cost=0 card=0)'
STAT #140146801009976 id=2 cnt=0 pid=1 pos=1 obj=0 op='MERGE JOIN ANTI (cr=0 cu=0 pr=0 pw=0 time=125 us cost=2 card=1)'
STAT #140146801009976 id=3 cnt=4 pid=2 pos=1 obj=0 op='MERGE SORT (cr=0 cu=0 pr=0 pw=0 time=55 us cost=0 card=0)'
STAT #140146801009976 id=4 cnt=4 pid=3 pos=1 obj=2653 op='TABLE ACCESS FULL TRACE_EVENT_10046_INDEXT_8 (cr=0 cu=1 pr=0 pw=0 time=23 us cost=1 card=4)'
STAT #140146801009976 id=5 cnt=7 pid=2 pos=2 obj=0 op='MERGE SORT (cr=0 cu=0 pr=0 pw=0 time=42 us cost=0 card=0)'
STAT #140146801009976 id=6 cnt=13 pid=3 pos=1 obj=2652 op='TABLE ACCESS FULL TRACE_EVENT_10046_INDEXT_7 (cr=0 cu=1 pr=0 pw=0 time=16 us cost=1 card=13)'
CLOSE #140146801009976 type=0,tim=1744861410712259
WAIT #140146801009976: nam='SQL**** message from client' ela= 3 tim=1744861410712449
```

## Disabling Tracing

If tracing for a certain event is no longer required, automatic tracing can be disabled as needed:

- EVENT_ID: Tracing event ID, a required parameter to specify the specific tracing event based on the ID value.
- OFF: This keyword disables the automatic tracing.

TRACE NAME CONTEXT, FOREVER: Only for syntax compatibility, with no actual significance.

```sql
-- Disable the current event tracing task
ALTER SESSION SET EVENTS='EVENT_ID TRACE NAME CONTEXT OFF';
    
--- The currently active tracing tasks remain unaffected, and subsequently created sessions will have tracing disabled by default.
ALTER SYSTEM SET EVENTS='';    
```
