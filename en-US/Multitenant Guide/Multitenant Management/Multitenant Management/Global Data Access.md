Global data access refers to the capability in CDBs to query all PDB data from the CDB root.

Based on this, DBAs can patrol and monitor status information of all PDBs under the CDB root, reducing redundant and cumbersome operations that require direct connection to each PDB to query corresponding data, making CDB operations more convenient and efficient.

## CDB Views

When YashanDB is deployed as a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), it provides a set of CDB views for DBAs to obtain global information from the CDB root. For example, querying the CDB_TABLES view can retrieve all tables in the current environment, and querying the CDB_ROLE_PRIVS view can retrieve all role-based authorization records in the current environment.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Query all user-defined created users
SELECT CON_ID,USERNAME,COMMON FROM CDB_USERS WHERE DATABASE_MAINTAINED = 'N';
      CON_ID USERNAME                                                         COMMON
------------ ---------------------------------------------------------------- ------
           2 SYS_PDB1                                                         N

-- Based on CON_ID, join queries can also be performed to directly return container names and actual data information
SELECT c.NAME AS CON_NAME, CDB_USERS.USERNAME, CDB_USERS.COMMON
FROM CDB_USERS
INNER JOIN V$CONTAINERS c ON CDB_USERS.CON_ID = c.CON_ID
WHERE DATABASE_MAINTAINED = 'N';
CON_NAME                                                         USERNAME                                                         COMMON
---------------------------------------------------------------- ---------------------------------------------------------------- ------
PDB1                                                             SYS_PDB1                                                         N
```

## ## CONTAINERS Function

The CONTAINERS function enables aggregation queries in the CDB root to retrieve relevant data from identically named query objects across all containers (the CDB root and all PDBs), implementing global query functionality, with the pseudo-column CON_ID generated in query results to distinguish data sources.

 

To query global aggregated data, the following requirements must be met:

- Appropriate permissions for all target query objects are required.

- The query objects in PDB must have the same table structure definition as the corresponding objects in the CDB root.

- If LOB type columns exist in the column items (select_list) of an aggregate query, the entire query result set will be NULL.

 

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Get PDB information
SELECT NAME,TYPE,STATUS FROM V$CONTAINERS;
NAME                                                             TYPE              STATUS
---------------------------------------------------------------- ----------------- -----------------
CDB$ROOT                                                         ROOT              OPEN
PDB$SEED                                                         SEED              CLOSED
PDB1                                                             PDB               OPEN


-- Globally query the v$parameter view on the CDB root to get the parameter configuration of all containers (the CDB root and all PDBs) 
SELECT con_id,name,value FROM containers(V$PARAMETER) WHERE name ='DB_BLOCK_SIZE';

      CON_ID NAME                                                  VALUE
------------ ----------------------------------------------------- ----------------------
           0 DB_BLOCK_SIZE                                         8192
           2 DB_BLOCK_SIZE                                         8192

-- Based on CON_ID, join queries can also be performed to directly return container names and actual data information
SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
FROM containers(V$PARAMETER) AS c
INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
WHERE c.NAME = 'DB_BLOCK_SIZE';
CON_NAME     PARAMETER                                             VALUE
------------ ----------------------------------------------------- ----------------------
CDB$ROOT     DB_BLOCK_SIZE                                         8192
PDB1         DB_BLOCK_SIZE                                         8192
```
