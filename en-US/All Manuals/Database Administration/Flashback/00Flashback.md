YashanDB Flashback Features Include: 

| Feature Item     | Description  | Dependencies | Applicability  |
|--------------------|-----------------|---------------------|-----------------|
| [Database Flashback](./全库闪回/00全库闪回) | Similar to PITR, restores the database to a past state | Flashback log files | Standalone Deployment & YAC Deployment |  
| [Table Flashback](./表闪回) | Restores deleted tables/partitions and their data (i.e., objects in the Recycle Bin), serving as the reverse operation of DROP, TRUNCATE, and ALTER TABLE TRUNCATE PARTITION | Recycle Bin | HEAP tables | 
| [Data Flashback](./数据闪回) | Query historical data of existing tables at a past timestamp (read-only) | UNDO logs | Standalone Deployment & YAC Deployment |
| | Restores existing table data to a past state, serving as the reverse operation of INSERT, DELETE, etc. | UNDO logs | HEAP tables |  
