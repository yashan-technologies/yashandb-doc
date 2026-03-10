The CDB views are basically isomorphic to DBA views, with CDB views containing a unique CON_ID (container ID) field to identify data sources, while other field descriptions can be referenced from DBA views.

CDB views are meaningful only in CDBs (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE). Querying CDB views in the CDB root retrieves global information, while querying CDB views in PDB only counts information from the current PDB.

|View Name |Functionality Description |
|--------------------------------|--------------------------------------------------------------------------|
| CDB_ALL_TABLES |  Information about all tables, including system tables                                       |
| CDB_CDB_RSRC_PLANS           |  Information about all container resource plans                                       |
| CDB_CDB_RSRC_PLAN_DIRECTIVES |  Information about all container resource plan directives                                       |
| CDB_CONSTRAINTS |  Information about all containers              |
| CDB_CONS_COLUMNS |  Information about constraint information for columns in all tables              |
| CDB_DATA_FILES |  Information about all data files              |
| CDB_INDEXES |  Information about all indexes               |
| CDB_IND_COLUMNS |  Information about all index columns              |
| CDB_IND_EXPRESSIONS |  Information about all function index expressions              |
| CDB_OBJECTS |  Information about all objects              |
| CDB_PROFILES |  Information about all user profiles              |
| CDB_ROLES |  Information about all roles              |
| CDB_ROLE_PRIVS |  Information about all role-based authorization records              |
| CDB_SYS_PRIVS |  Information about all system privilege-based authorization records              |
| CDB_TABLES |  Information about all tables, including system tables              |
| CDB_TABLESPACES |  Information about all tablespaces              |
| CDB_TAB_COLS |  Information about all columns in all tables, including virtual columns created internally by the system         |
| CDB_TAB_COLUMNS |  Information about all columns in all tables             |
| CDB_TAB_PRIVS |  Information about all object privilege-based authorization records             |
| CDB_TS_QUOTAS |  Information about tablespace quotas for all users and quota usage              |
| CDB_USERS |  Information about all users             |
