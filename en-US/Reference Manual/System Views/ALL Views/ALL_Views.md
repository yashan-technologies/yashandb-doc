The views listed in this document are essentially isomorphic to the DBA views. Please refer to the corresponding DBA views for field descriptions.

|View Name |Functionality Description |
|-------------------------------|---------------------------------------------------------------------|
| ALL\_TABLES                       | Information about all tables accessible to the current user                            |
| ALL\_ALL\_TABLES                  | Information about all tables accessible to the current user                            |
| ALL\_INDEXES                      | Information about all indexes accessible to the current user                           |
| ALL\_MVIEWS                       | Information about all materialized views accessible to the current user                |
| ALL\_TAB\_COLS                    | Information about all columns accessible to the current user                           |
| ALL\_TAB\_IDENTITY\_COLS          | Information about all identity columns accessible to the current user                  |
| ALL\_TAB\_PARTITIONS              | Information about all table partition information accessible to the current user       |
| ALL\_IND\_PARTITIONS              | Information about all index partition information accessible to the current user       |
| ALL\_PART\_TABLES                 | Information about all partition tables accessible to the current user                  |
| ALL\_PART\_INDEXES                | Information about all partition indexes accessible to the current user                 |
| ALL\_VIEWS                        | Information about all views accessible to the current user                             |
| ALL\_SEQUENCES                    | Information about all sequences accessible to the current user                         |
| ALL\_LOBS                         | Information about all LOBs accessible to the current user                             |
| ALL\_LOB\_PARTITIONS              | Information about all LOB partition information accessible to the current user        |
| ALL\_PART\_KEY\_COLUMNS           | Information about all partition key columns accessible to the current user            |
| ALL\_SUBPART\_KEY\_COLUMNS        | Information about all sub-partition key columns accessible to the current user        |
| ALL\_OBJECTS                      | Information about all objects accessible to the current user                          |
| ALL\_SYNONYMS                     | Information about all synonyms accessible to the current user                         |
| ALL\_CONSTRAINTS                  | Information about all constraints accessible to the current user                      |
| ALL\_TAB\_COLUMNS                 | Information about all columns accessible to the current user; this view is similar to ALL_TAB_COLS but does not display system-generated columns |
| ALL\_TAB\_COMMENTS                | Information about all table comments accessible to the current user                   |
| ALL\_COL\_COMMENTS                | Information about all column comments accessible to the current user                  |
| ALL\_TAB\_ENCRYPTED               | Information about all encrypted table information accessible to the current user      |
| ALL\_DEPENDENCIES                 | Information about all dependencies accessible to the current user                     |
| ALL\_PROCEDURES                   | Information about all procedure bodies accessible to the current user                 |
| ALL\_ARGUMENTS                    | Information about all procedure parameters accessible to the current user              |
| ALL\_LSC\_SLICE\_STAT             | Information about all LSC tables' SLICE-related information accessible to the current user |
| ALL\_LSC\_TABLE\_COMPRESSION      | Information about all LSC table compression information accessible to the current user |
| ALL\_LSC\_COLUMN\_COMPRESSION     | Information about the compression details of each column in all LSC tables accessible to the current user |
| ALL\_TRIGGERS                     | Information about all triggers accessible to the current user                         |
| ALL\_TRIGGER\_COLS                | Information about column usage in all triggers accessible to the current user         |
| ALL\_TRIGGER\_ORDERING            | Information about all triggers that have a FOLLOWS or PRECEDES order accessible to the current user |
| ALL\_TAB\_STATISTICS              | Statistics for tables accessible to the current user                                  |
| ALL\_IND\_STATISTICS              | Statistics for indexes accessible to the current user                                 |
| ALL\_TAB\_COL\_STATISTICS         | Statistics for columns in tables accessible to the current user                       |
| ALL\_HISTOGRAMS                   | Histogram information for columns accessible to the current user                      |
| ALL\_TAB\_HISTOGRAMS              | Histogram information for columns accessible to the current user                      |
| ALL\_PART\_COL\_STATISTICS        | Statistics for partition columns accessible to the current user                       |
| ALL\_PART\_HISTOGRAMS             | Histogram information for partition columns accessible to the current user            |
| ALL\_LOG\_GROUPS                  | Additional log information accessible to the current user                             |
| ALL\_JOBS                         | Job information created by the DBMS_JOB package accessible to the current user       |
| ALL\_SCHEDULER\_JOBS              | Job information created by the DBMS_SCHEDULER package accessible to the current user |
| ALL\_PART\_STORE                  | Information about the tablespace of partition tables accessible to the current user   |
| ALL\_CONS\_COLUMNS                | Information about all constraint columns accessible to the current user               |
| ALL\_IND\_COLUMNS                 | Information about all index columns accessible to the current user                   |
| ALL\_IND\_EXPRESSIONS             | Information about all function index expressions accessible to the current user       |
| ALL\_RECYCLEBIN                    | Information about all objects in the recycle bin accessible to the current user      |
| ALL\_SOURCE                        | Information about all source code accessible to the current user                     |
| ALL\_TAB\_privs                   | Information about all table privileges accessible to the current user                  |
| ALL\_USERS                        | Information about all users accessible to the current user                           |
| ALL\_TAB\_MODIFICATIONS           | Information on modifications made to tables accessible to the current user           |
| ALL\_TYPES                        | Information about all UDTs accessible to the current user                            |
| ALL\_COLL\_TYPES                  | Information about composite types in all UDTs accessible to the current user        |
| ALL\_COLL\_TYPE\_INFO             | Internal information about composite types in all UDTs accessible to the current user |
| ALL\_TYPE\_ATTRS                  | Information about object type attributes in all UDTs accessible to the current user |
| ALL\_TYPE\_ATTR\_INFO             | Internal information about object type attributes in all UDTs accessible to the current user |
| ALL\_TYPE\_METHODS                | Information about object type methods in all UDTs accessible to the current user     |
| ALL\_TAB\_STAT\_PREFS             | Statistics options for tables accessible to the current user                          |
| ALL\_NESTED\_TABLES               | Information about all nested tables accessible to the current user                   |
| ALL\_TAB\_SUBPARTITIONS           | Information about all subpartitions accessible to the current user                   |
| ALL\_SUBPARTITION\_TEMPLATES      | Information about all subpartition templates accessible to the current user           |
| ALL\_IND\_SUBPARTITIONS           | Information about all index subpartitions accessible to the current user             |
| ALL\_LOB\_SUBPARTITIONS           | Information about all LOB subpartitions accessible to the current user               |
| ALL\_DIST\_TABLES                 | Information about all sharded tables accessible to the current user (deprecated since v23.2.1) |
| ALL\_DIST\_KEY\_COLUMNS          | Information about all distribution key columns accessible to the current user (deprecated since v23.2.1) |
| ALL\_SORT\_TABLES                 | Information about sorting of all tables accessible to the current user               |
| ALL\_SORT\_KEY\_COLUMNS          | Information about all sort key columns accessible to the current user                |
| ALL\_ACS                           | Information about all access control systems accessible to the current user           |
| ALL\_AC\_COLUMNS                  | Information about all access control columns accessible to the current user           |
| ALL\_DB\_LINKS                    | Information about all database links accessible to the current user                   |
| ALL\_DIRECTORIES                   | Information about all directory objects accessible to the current user                |
| ALL\_EXTERNAL\_TABLES             | Information about all external tables accessible to the current user                  |
| ALL\_EXTERNAL\_LOCATIONS          | Information about all external table locations accessible to the current user         |
| ALL\_AC\_PARTITIONS               | Information about all access control partitions accessible to the current user        |
| ALL\_SA\_TABLE\_POLICIES           | Information about LBAC label policy association tables accessible to the current user |
| ALL\_OUTLINES                     | Information about all outlines accessible to the current user                         |
| ALL\_UPDATABLE\_COLUMNS           | Information about columns that can be updated accessible to the current user         |
| ALL\_ENCRYPTED\_COLUMNS           | Information about all encrypted columns accessible to the current user               |
| ALL\_HIST\_CHECK\_INFO            | Information about tamper-proof objects that have anti-tamper capabilities enabled accessible to the current user |
| ALL\_CONTEXT                      | Information about all context objects with set values accessible to the current user  |
| ALL\_OBJECT\_AFFINITIES           | Information about affinity of all objects accessible to the current user             |
