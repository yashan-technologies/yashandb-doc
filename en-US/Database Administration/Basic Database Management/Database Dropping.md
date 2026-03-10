To delete a database instance, execute the delete statement [DROP DATABASE](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/DROP DATABASE) to remove all data files of that database instance. After the database is deleted, use the [CREATE DATABASE](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE DATABASE) statement to recreate a new database.

The DROP DATABASE statement is not suitable for ISC Distributed Cluster Deployment.

## Constraints

- The database is in NOMOUNT mode, and the database data files are complete and can start properly.
- YashanDB uses Standalone Deployment or YAC Deployment.

## Steps

> **Warn**:
>
> Deleting a database will permanently remove all related files of that database. Please proceed with caution.

1. Restart the instance to NOMOUNT mode.

```sql
SHUTDOWN;
exit

$ yasboot cluster start -c yashandb -m nomount
```

2. Log in to the system as the sys user and delete the database.

```sql
$ yasql sys/********

-- Delete all database files except for archived files
SQL> DROP DATABASE;

-- Delete all database files including archived files
DROP DATABASE including ARCHIVELOG;
```
