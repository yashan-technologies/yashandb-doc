YashanDB Flashback Features Include: 

| Feature Item     | Description  | Dependencies | Applicability  |
|--------------------|-----------------|---------------------|-----------------|
| [Database Flashback](./Database Flashback/00Database Flashback) | Similar to PITR, restores the database to a past state | Flashback log files | Standalone Deployment & YAC/Distributed Cluster Deployment |  
| [Table Flashback](./Recycle Bin Object Flashback/00Recycle Bin Object Flashback) | Restores deleted tables/partitions and their data (i.e., objects in the Recycle Bin), serving as the reverse operation of DROP, TRUNCATE, and ALTER TABLE TRUNCATE PARTITION | Recycle Bin | HEAP tables | 
| [Data Flashback](./UNDO Data Flashback) | Query historical data of existing tables at a past timestamp (read-only) | UNDO logs | Standalone Deployment & YAC/Distributed Cluster Deployment |
| | Restores existing table data to a past state, serving as the reverse operation of INSERT, DELETE, etc. | UNDO logs | HEAP tables |  
