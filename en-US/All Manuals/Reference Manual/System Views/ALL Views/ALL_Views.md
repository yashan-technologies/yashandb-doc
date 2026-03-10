The views listed in this document are essentially isomorphic to the DBA views. Please refer to the corresponding DBA views for field descriptions.

|View Name |Functionality Description |
|-------------------------------|---------------------------------------------------------------------|
| ALL_TABLES                       | Information about all tables accessible to the current user                            |
| ALL_ALL_TABLES                  | Information about all tables accessible to the current user                            |
| ALL_INDEXES                      | Information about all indexes accessible to the current user                           |
| ALL_MVIEWS                       | Information about all materialized views accessible to the current user                |
| ALL_TAB_COLS                    | Information about all columns accessible to the current user                           |
| ALL_TAB_IDENTITY_COLS          | Information about all identity columns accessible to the current user                  |
| ALL_TAB_PARTITIONS              | Information about all table partition information accessible to the current user       |
| ALL_IND_PARTITIONS              | Information about all index partition information accessible to the current user       |
| ALL_PART_TABLES                 | Information about all partition tables accessible to the current user                  |
| ALL_PART_INDEXES                | Information about all partition indexes accessible to the current user                 |
| ALL_VIEWS                        | Information about all views accessible to the current user                             |
| ALL_SEQUENCES                    | Information about all sequences accessible to the current user                         |
| ALL_LOBS                         | Information about all LOBs accessible to the current user                             |
| ALL_LOB_PARTITIONS              | Information about all LOB partition information accessible to the current user        |
| ALL_PART_KEY_COLUMNS           | Information about all partition key columns accessible to the current user            |
| ALL_SUBPART_KEY_COLUMNS        | Information about all sub-partition key columns accessible to the current user        |
| ALL_OBJECTS                      | Information about all objects accessible to the current user                          |
| ALL_SYNONYMS                     | Information about all synonyms accessible to the current user                         |
| ALL_CONSTRAINTS                  | Information about all constraints accessible to the current user                      |
| ALL_TAB_COLUMNS                 | Information about all columns accessible to the current user; this view is similar to ALL_TAB_COLS but does not display system-generated columns |
| ALL_TAB_COMMENTS                | Information about all table comments accessible to the current user                   |
| ALL_COL_COMMENTS                | Information about all column comments accessible to the current user                  |
| ALL_TAB_ENCRYPTED               | Information about all encrypted table information accessible to the current user      |
| ALL_DEPENDENCIES                 | Information about all dependencies accessible to the current user                     |
| ALL_PROCEDURES                   | Information about all procedure bodies accessible to the current user                 |
| ALL_ARGUMENTS                    | Information about all procedure parameters accessible to the current user              |
| ALL_LSC_SLICE_STAT             | Information about all LSC tables' SLICE-related information accessible to the current user |
| ALL_LSC_TABLE_COMPRESSION      | Information about all LSC table compression information accessible to the current user |
| ALL_LSC_COLUMN_COMPRESSION     | Information about the compression details of each column in all LSC tables accessible to the current user |
| ALL_TRIGGERS                     | Information about all triggers accessible to the current user                         |
| ALL_TRIGGER_COLS                | Information about column usage in all triggers accessible to the current user         |
| ALL_TRIGGER_ORDERING            | Information about all triggers that have a FOLLOWS or PRECEDES order accessible to the current user |
| ALL_TAB_STATISTICS              | Statistics for tables accessible to the current user                                  |
| ALL_IND_STATISTICS              | Statistics for indexes accessible to the current user                                 |
| ALL_TAB_COL_STATISTICS         | Statistics for columns in tables accessible to the current user                       |
| ALL_HISTOGRAMS                   | Histogram information for columns accessible to the current user                      |
| ALL_TAB_HISTOGRAMS              | Histogram information for columns accessible to the current user                      |
| ALL_PART_COL_STATISTICS        | Statistics for partition columns accessible to the current user                       |
| ALL_PART_HISTOGRAMS             | Histogram information for partition columns accessible to the current user            |
| ALL_LOG_GROUPS                  | Additional log information accessible to the current user                             |
| ALL_JOBS                         | Job information created by the DBMS_JOB package accessible to the current user       |
| ALL_SCHEDULER_JOBS              | Job information created by the DBMS_SCHEDULER package accessible to the current user |
| ALL_PART_STORE                  | Information about the tablespace of partition tables accessible to the current user   |
| ALL_CONS_COLUMNS                | Information about all constraint columns accessible to the current user               |
| ALL_IND_COLUMNS                 | Information about all index columns accessible to the current user                   |
| ALL_IND_EXPRESSIONS             | Information about all function index expressions accessible to the current user       |
| ALL_RECYCLEBIN                    | Information about all objects in the recycle bin accessible to the current user      |
| ALL_SOURCE                        | Information about all source code accessible to the current user                     |
| ALL_TAB_PRIVS                   | Information about all table privileges accessible to the current user                  |
| ALL_USERS                        | Information about all users accessible to the current user                           |
| ALL_TAB_MODIFICATIONS           | Information on modifications made to tables accessible to the current user           |
| ALL_TYPES                        | Information about all UDTs accessible to the current user                            |
| ALL_COLL_TYPES                  | Information about composite types in all UDTs accessible to the current user        |
| ALL_COLL_TYPE_INFO             | Internal information about composite types in all UDTs accessible to the current user |
| ALL_TYPE_ATTRS                  | Information about object type attributes in all UDTs accessible to the current user |
| ALL_TYPE_ATTR_INFO             | Internal information about object type attributes in all UDTs accessible to the current user |
| ALL_TYPE_METHODS                | Information about object type methods in all UDTs accessible to the current user     |
| ALL_TAB_STAT_PREFS             | Statistics options for tables accessible to the current user                          |
| ALL_NESTED_TABLES               | Information about all nested tables accessible to the current user                   |
| ALL_TAB_SUBPARTITIONS           | Information about all subpartitions accessible to the current user                   |
| ALL_SUBPARTITION_TEMPLATES      | Information about all subpartition templates accessible to the current user           |
| ALL_IND_SUBPARTITIONS           | Information about all index subpartitions accessible to the current user             |
| ALL_LOB_SUBPARTITIONS           | Information about all LOB subpartitions accessible to the current user               |
| ALL_DIST_TABLES                 | Information about all sharded tables accessible to the current user (deprecated since v23.2.1) |
| ALL_DIST_KEY_COLUMNS          | Information about all distribution key columns accessible to the current user (deprecated since v23.2.1) |
| ALL_SORT_TABLES                 | Information about sorting of all tables accessible to the current user               |
| ALL_SORT_KEY_COLUMNS          | Information about all sort key columns accessible to the current user                |
| ALL_ACS                           | Information about all access control systems accessible to the current user           |
| ALL_AC_COLUMNS                  | Information about all access control columns accessible to the current user           |
| ALL_DB_LINKS                    | Information about all database links accessible to the current user                   |
| ALL_DIRECTORIES                   | Information about all directory objects accessible to the current user                |
| ALL_EXTERNAL_TABLES             | Information about all external tables accessible to the current user                  |
| ALL_EXTERNAL_LOCATIONS          | Information about all external table locations accessible to the current user         |
| ALL_AC_PARTITIONS               | Information about all access control partitions accessible to the current user        |
| ALL_SA_TABLE_POLICIES           | Information about LBAC security policy association tables accessible to the current user |
| ALL_OUTLINES                     | Information about all outlines accessible to the current user                         |
| ALL_UPDATABLE_COLUMNS           | Information about columns that can be updated accessible to the current user         |
| ALL_ENCRYPTED_COLUMNS           | Information about all encrypted columns accessible to the current user               |
| ALL_HIST_CHECK_INFO            | Information about tamper-proof objects that have anti-tamper capabilities enabled accessible to the current user |
| ALL_CONTEXT                      | Information about all context objects with set values accessible to the current user  |