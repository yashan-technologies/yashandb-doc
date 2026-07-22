The USER views are basically isomorphic to the DBA views, and the field descriptions refer to the DBA views.

In an ISC Distributed Cluster Deployment, the USER views also only collect information relevant to the current node, similar to the DBA views.

|View Name |Functionality Description |
|--------------------------------|--------------------------------------------------------------------------|
| USER\_TABLES                   | Information about the current user's tables                                      |
| USER\_ALL\_TABLES              | Information about the current user's tables                                      |
| USER\_INDEXES                  | Information about the current user's indexes                                     |
| USER\_MVIEWS                   | Information about the current user's materialized views                          |
| USER\_TAB\_COLS                | Information about the current user's columns                                     |
| USER\_TAB\_IDENTITY\_COLS      | Information about the current user's identity columns                             |
| USER\_TAB\_PARTITIONS          | Information about the current user's table partitions                             |
| USER\_IND\_PARTITIONS          | Information about the current user's index partitions                             |
| USER\_PART\_TABLES             | Information about the current user's partition tables                             |
| USER\_PART\_INDEXES            | Information about the current user's partition indexes                            |
| USER\_SEGMENTS                 | Information about the current user's segments                                     |
| USER\_VIEWS                    | Information about the current user's views                                        |
| USER\_SEQUENCES                | Information about the current user's sequences                                    |
| USER\_LOBS                     | Information about the current user's LOBs                                         |
| USER\_LOB\_PARTITIONS          | Information about the current user's LOB partitions                               |
| USER\_PART\_KEY\_COLUMNS       | Information about the current user's partition key columns                         |
| USER\_SUBPART\_KEY\_COLUMNS    | Information about all secondary partition key columns for the current user       |
| USER\_OBJECTS                  | Information about the current user's objects                                      |
| USER\_SYNONYMS                 | Information about the current user's synonyms                                     |
| USER\_USERS                    | Information about the current user                                               |
| USER\_CONSTRAINTS              | Information about the current user's constraints                                  |
| USER\_TAB\_COMMENTS            | Information about comments on the current user's tables                          |
| USER\_COL\_COMMENTS            | Information about comments on the current user's columns                         |
| USER\_TAB\_ENCRYPTED           | Information about encrypted tables for the current user                          |
| USER\_DEPENDENCIES             | Information about dependencies for the current user                              |
| USER\_PROCEDURES               | Information about procedures for the current user                                 |
| USER\_ARGUMENTS                | Information about procedure parameters for the current user                       |
| USER\_LSC\_SLICE\_STAT         | Information about all LSC table SLICE related data for the current user         |
| USER\_LSC\_TABLE\_COMPRESSION  | Information about compression for all LSC tables accessible by the current user |
| USER\_LSC\_COLUMN\_COMPRESSION | Information about column compression for all LSC tables accessible by the current user |
| USER\_TRIGGERS                 | Information about all triggers for the current user                              |
| USER\_TRIGGER\_COLS            | Information about columns used in all triggers for the current user             |
| USER\_TRIGGER\_ORDERING        | Information about all triggers with FOLLOWS or PRECEDES order for the current user |
| USER\_TAB\_STATISTICS          | Statistics for the current user's tables                                         |
| USER\_IND\_STATISTICS          | Statistics for the current user's indexes                                        |
| USER\_TAB\_COL\_STATISTICS     | Statistics for the current user's columns                                        |
| USER\_HISTOGRAMS               | Information about histograms for the current user                                |
| USER\_TAB\_HISTOGRAMS          | Information about histograms for the current user                                |
| USER\_PART\_COL\_STATISTICS    | Statistics for partition columns of the current user                             |
| USER\_PART\_HISTOGRAMS         | Information about histograms for partition columns of the current user           |
| USER\_LOG\_GROUPS              | Information about supplemental log for the current user                          |
| USER\_OUTLINES                 | Information about outlines for the current user                                  |
| USER\_OUTLINE\_HINTS           | Information about hints corresponding to outlines for the current user          |
| USER\_JOBS                     | Information about jobs created by the current user's DBMS_JOBS package           |
| USER\_SCHEDULER\_JOBS          | Information about jobs created by the current user's DBMS_SCHEDULER package      |
| USER\_PART\_STORE              | Information about tablespace for extended partition tables of the current user   |
| USER\_CONS\_COLUMNS            | Information about constraint columns for the current user                        |
| USER\_IND\_COLUMNS             | Information about index columns for the current user                             |
| USER\_IND\_EXPRESSIONS         | Information about function index expressions for the current user               |
| USER\_RECYCLEBIN                | Information about the current user's recycle bin                                 |
| USER\_SOURCE                    | Information about the current user's resources                                    |
| USER\_SYS\_PRIVS               | Information about system privileges for the current user                         |
| USER\_ROLE\_PRIVS              | Information about roles for the current user                                     |
| ROLE\_ROLE\_PRIVS              | Role information for roles owned by the current user, not applicable in ISC Distributed Cluster Deployment |
| ROLE\_SYS\_PRIVS               | System-level privilege information for roles owned by the current user, not applicable in ISC Distributed Cluster Deployment |
| ROLE\_TAB\_PRIVS               | Object-level privilege information for roles owned by the current user, not applicable in ISC Distributed Cluster Deployment |
| USER\_TABLESPACES               | Information about the current user's tablespaces                                  |
| USER\_TAB\_COLUMNS              | Information about columns of the current user's tables                           |
| USER\_TAB\_PRIVS               | Privilege information for the current user's tables                              |
| USER\_TAB\_MODIFICATIONS        | Modification status of the current user's tables                                 |
| USER\_TYPES                     | Information about all UDTs of the current user                                   |
| USER\_COL\_TYPES                | Information about collection types in all UDTs of the current user              |
| USER\_COL\_TYPE\_INFO          | Internal information about collection types in all UDTs of the current user     |
| USER\_TYPE\_ATTRS              | Information about object type attributes in all UDTs of the current user        |
| USER\_TYPE\_ATTR\_INFO         | Internal information about object type attributes in all UDTs of the current user |
| USER\_TYPE\_METHODS            | Information about methods in object types for all UDTs of the current user      |
| USER\_TAB\_STAT\_PREFS         | Statistics options for the current user's tables                                 |
| USER\_NESTED\_TABLES           | Information about all nested tables for the current user                        |
| USER\_TAB\_SUBPARTITIONS       | Information about all subpartitions for the current user                        |
| USER\_SUBPARTITION\_TEMPLATES  | Information about all subpartition templates for the current user                |
| USER\_IND\_SUBPARTITIONS       | Information about all index subpartitions for the current user                  |
| USER\_LOB\_SUBPARTITIONS       | Information about all LOB subpartitions for the current user                    |
| USER\_DIST\_TABLES             | Information about all sharded tables for the current user (deprecated since v23.2.1) |
| USER\_DIST\_KEY\_COLUMNS       | Information about all distribution key columns for the current user (deprecated since v23.2.1) |
| USER\_SORT\_TABLES             | Information about sorting for all tables of the current user                     |
| USER\_SORT\_KEY\_COLUMNS       | Information about all sort key columns for the current user                     |
| USER\_ACS                      | Information about all ACs for the current user                                   |
| USER\_AC\_COLUMNS              | Information about all AC columns for the current user                           |
| USER\_DB\_LINKS                | Information about all database links for the current user                       |
| USER\_EXTERNAL\_TABLES         | Information about all external tables for the current user                      |
| USER\_EXTERNAL\_LOCATIONS      | Information about all external table locations for the current user             |
| COL                             | Column information for the current user's tables and views; similar in meaning to USER_TAB_COLS, but includes columns in nested tables, ownership information for UDT data types, and does not display hidden columns |
| USER\_AC\_PARTITIONS           | Information about AC partitions for the current user                            |
| USER\_UPDATABLE\_COLUMNS       | Information about updatable columns accessible to the current user               |
| USER\_ENCRYPTED\_COLUMNS       | Information about encrypted columns for the current user                        |
| USER\_EXTENTS                  | Information about extents within segments for the current user                  |
| USER\_HIST\_CHECK\_INFO        | Information about tamper-proof objects with tamper-proof capabilities accessible to the current user |
| USER\_UNIFIED\_AUDIT\_TRAIL    | Information about all audit records for the current user                       |
| USER\_OBJECT\_AFFINITIES       | Information about affinity of all objects for the current user                 |