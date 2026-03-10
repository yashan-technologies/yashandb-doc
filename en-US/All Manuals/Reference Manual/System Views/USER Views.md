The USER views are basically isomorphic to the DBA views, and the field descriptions refer to the DBA views.

In an ISC Distributed Cluster Deployment, the USER views also only collect information relevant to the current node, similar to the DBA views.

|View Name |Functionality Description |
|--------------------------------|--------------------------------------------------------------------------|
| USER_TABLES                   | Information about the current user's tables                                      |
| USER_ALL_TABLES              | Information about the current user's tables                                      |
| USER_INDEXES                  | Information about the current user's indexes                                     |
| USER_MVIEWS                   | Information about the current user's materialized views                          |
| USER_TAB_COLS                | Information about the current user's columns                                     |
| USER_TAB_IDENTITY_COLS      | Information about the current user's identity columns                             |
| USER_TAB_PARTITIONS          | Information about the current user's table partitions                             |
| USER_IND_PARTITIONS          | Information about the current user's index partitions                             |
| USER_PART_TABLES             | Information about the current user's partition tables                             |
| USER_PART_INDEXES            | Information about the current user's partition indexes                            |
| USER_SEGMENTS                 | Information about the current user's segments                                     |
| USER_VIEWS                    | Information about the current user's views                                        |
| USER_SEQUENCES                | Information about the current user's sequences                                    |
| USER_LOBS                     | Information about the current user's LOBs                                         |
| USER_LOB_PARTITIONS          | Information about the current user's LOB partitions                               |
| USER_PART_KEY_COLUMNS       | Information about the current user's partition key columns                         |
| USER_SUBPART_KEY_COLUMNS    | Information about all secondary partition key columns for the current user       |
| USER_OBJECTS                  | Information about the current user's objects                                      |
| USER_SYNONYMS                 | Information about the current user's synonyms                                     |
| USER_USERS                    | Information about the current user                                               |
| USER_CONSTRAINTS              | Information about the current user's constraints                                  |
| USER_TAB_COMMENTS            | Information about comments on the current user's tables                          |
| USER_COL_COMMENTS            | Information about comments on the current user's columns                         |
| USER_TAB_ENCRYPTED           | Information about encrypted tables for the current user                          |
| USER_DEPENDENCIES             | Information about dependencies for the current user                              |
| USER_PROCEDURES               | Information about procedures for the current user                                 |
| USER_ARGUMENTS                | Information about procedure parameters for the current user                       |
| USER_LSC_SLICE_STAT         | Information about all LSC table SLICE related data for the current user         |
| USER_LSC_TABLE_COMPRESSION  | Information about compression for all LSC tables accessible by the current user |
| USER_LSC_COLUMN_COMPRESSION | Information about column compression for all LSC tables accessible by the current user |
| USER_TRIGGERS                 | Information about all triggers for the current user                              |
| USER_TRIGGER_COLS            | Information about columns used in all triggers for the current user             |
| USER_TRIGGER_ORDERING        | Information about all triggers with FOLLOWS or PRECEDES order for the current user |
| USER_TAB_STATISTICS          | Statistics for the current user's tables                                         |
| USER_IND_STATISTICS          | Statistics for the current user's indexes                                        |
| USER_TAB_COL_STATISTICS     | Statistics for the current user's columns                                        |
| USER_HISTOGRAMS               | Information about histograms for the current user                                |
| USER_TAB_HISTOGRAMS          | Information about histograms for the current user                                |
| USER_PART_COL_STATISTICS    | Statistics for partition columns of the current user                             |
| USER_PART_HISTOGRAMS         | Information about histograms for partition columns of the current user           |
| USER_LOG_GROUPS              | Information about supplemental log for the current user                          |
| USER_OUTLINES                 | Information about outlines for the current user                                  |
| USER_OUTLINE_HINTS           | Information about hints corresponding to outlines for the current user          |
| USER_JOBS                     | Information about jobs created by the current user's DBMS_JOBS package           |
| USER_SCHEDULER_JOBS          | Information about jobs created by the current user's DBMS_SCHEDULER package      |
| USER_PART_STORE              | Information about tablespace for extended partition tables of the current user   |
| USER_CONS_COLUMNS            | Information about constraint columns for the current user                        |
| USER_IND_COLUMNS             | Information about index columns for the current user                             |
| USER_IND_EXPRESSIONS         | Information about function index expressions for the current user               |
| USER_RECYCLEBIN                | Information about the current user's recycle bin                                 |
| USER_SOURCE                    | Information about the current user's resources                                    |
| USER_SYS_PRIVS               | Information about system privileges for the current user                         |
| USER_ROLE_PRIVS              | Information about roles for the current user                                     |
| ROLE_ROLE_PRIVS              | Role information for roles owned by the current user, not applicable in ISC Distributed Cluster Deployment |
| ROLE_SYS_PRIVS               | System-level privilege information for roles owned by the current user, not applicable in ISC Distributed Cluster Deployment |
| ROLE_TAB_PRIVS               | Object-level privilege information for roles owned by the current user, not applicable in ISC Distributed Cluster Deployment |
| USER_TABLESPACES               | Information about the current user's tablespaces                                  |
| USER_TAB_COLUMNS              | Information about columns of the current user's tables                           |
| USER_TAB_PRIVS               | Privilege information for the current user's tables                              |
| USER_TAB_MODIFICATIONS        | Modification status of the current user's tables                                 |
| USER_TYPES                     | Information about all UDTs of the current user                                   |
| USER_COL_TYPES                | Information about collection types in all UDTs of the current user              |
| USER_COL_TYPE_INFO          | Internal information about collection types in all UDTs of the current user     |
| USER_TYPE_ATTRS              | Information about object type attributes in all UDTs of the current user        |
| USER_TYPE_ATTR_INFO         | Internal information about object type attributes in all UDTs of the current user |
| USER_TYPE_METHODS            | Information about methods in object types for all UDTs of the current user      |
| USER_TAB_STAT_PREFS         | Statistics options for the current user's tables                                 |
| USER_NESTED_TABLES           | Information about all nested tables for the current user                        |
| USER_TAB_SUBPARTITIONS       | Information about all subpartitions for the current user                        |
| USER_SUBPARTITION_TEMPLATES  | Information about all subpartition templates for the current user                |
| USER_IND_SUBPARTITIONS       | Information about all index subpartitions for the current user                  |
| USER_LOB_SUBPARTITIONS       | Information about all LOB subpartitions for the current user                    |
| USER_DIST_TABLES             | Information about all sharded tables for the current user (deprecated since v23.2.1) |
| USER_DIST_KEY_COLUMNS       | Information about all distribution key columns for the current user (deprecated since v23.2.1) |
| USER_SORT_TABLES             | Information about sorting for all tables of the current user                     |
| USER_SORT_KEY_COLUMNS       | Information about all sort key columns for the current user                     |
| USER_ACS                      | Information about all ACs for the current user                                   |
| USER_AC_COLUMNS              | Information about all AC columns for the current user                           |
| USER_DB_LINKS                | Information about all database links for the current user                       |
| USER_EXTERNAL_TABLES         | Information about all external tables for the current user                      |
| USER_EXTERNAL_LOCATIONS      | Information about all external table locations for the current user             |
| COL                             | Column information for the current user's tables and views; similar in meaning to USER_TAB_COLS, but includes columns in nested tables, ownership information for UDT data types, and does not display hidden columns |
| USER_AC_PARTITIONS           | Information about AC partitions for the current user                            |
| USER_UPDATABLE_COLUMNS       | Information about updatable columns accessible to the current user               |
| USER_ENCRYPTED_COLUMNS       | Information about encrypted columns for the current user                        |
| USER_EXTENTS                  | Information about extents within segments for the current user                  |
| USER_HIST_CHECK_INFO        | Information about tamper-proof objects with tamper-proof capabilities accessible to the current user |
| USER_UNIFIED_AUDIT_TRAIL    | Information about all audit records for the current user                       |