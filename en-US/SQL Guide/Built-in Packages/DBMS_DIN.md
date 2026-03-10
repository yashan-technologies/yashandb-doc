The DBMS_DIN package provides a set of built-in stored procedures for detecting response performance at the distributed internal network node level and link level.

> **Note**:
>
> - When invoking any subprograms under the DBMS_DIN advanced package, you must connect to the database as the SYS user; otherwise, an error will occur.
> - The DBMS_DIN advanced package only supports the ISC Distributed Cluster Deployment form; executing it in a Standalone/YAC/Distributed Cluster form will result in an error.

## TRACE\_TRIGGER

```plsql
DBMS_DIN.TRACE_TRIGGER(
    NODE_ID        IN INTEGER DEFAULT -1,
    LINK_LEVEL     IN SMALLINT DEFAULT -1,
    LINK_ID        IN SMALLINT DEFAULT -1,
    PACK_SIZE    IN INTEGER DEFAULT 28);
```

The TRACE_TRIGGER program is used to trigger the distributed internal network for link-level response speed detection. It allows specification of particular nodes or links for detection and the size of extra data packets carried during the detection through input parameters.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| NODE_ID     | Communication node ID; default value is -1, meaning detection is sent to all active nodes by default. |
| LINK_LEVEL  | Communication channel level; default value is -1, meaning detection is sent to all channels by default. |
| LINK_ID     | Communication link ID; default value is -1, meaning detection is sent to all active links of the channel by default. |
| PACK_SIZE   | Size of the data packet carried during detection (unit: bytes); must not exceed 32KB and not less than 28 bytes. |

Usage Instructions:

-  If NODE_ID is specified, detection will only be for the specified node, and you cannot specify itself or an invalid node.
- If LINK_LEVEL and LINK_ID are specified, detection will only be for the specified link of the specified channel; LEVEL 0-2 corresponds to Internal Channel (INNER), Control Channel (CONTROL), and Data Channel (DATA) respectively.
- NODE_ID, LINK_LEVEL, and LINK_ID can be referenced based on the query results from the views [V/$DIN_LINK](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$DIN_LINK) and [GV$DIN_LINK](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/GV$DIN_LINK).
- Receiving links do not have detection capability and will neither execute nor report an error.

***Example*** for ISC Distributed Cluster Deployment

```plsql
-- No specific detection node, link, or data packet size specified, using default values
EXEC DBMS_DIN.TRACE_TRIGGER();
-- Specify the size of the data packet
EXEC DBMS_DIN.TRACE_TRIGGER(
    PACK_SIZE => 1024);
-- Specify detection of a specific node
EXEC DBMS_DIN.TRACE_TRIGGER(
    NODE_ID => 3);
-- Specify detection of a specific link of a specific node
EXEC DBMS_DIN.TRACE_TRIGGER(
    NODE_ID => 3,
    LINK_LEVEL => 1,
    LINK_ID => 1);
```
