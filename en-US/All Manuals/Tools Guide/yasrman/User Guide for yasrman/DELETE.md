DELETE is used to delete a specified backup set when the primary database in a Standalone Deployment or the ISC Distributed Cluster Deployment is running normally.

```ebnf+diagram
syntax::= DELETE BACKUPSET [IF EXISTS] TAG tag_name
```
If the specified tag_name does not exist, this command will return an error.

Specifying IF EXISTS means that if the backup set exists, it will attempt to delete it, and it will return a successful deletion regardless of whether tag_name exists.

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "DELETE BACKUPSET tag 'full_1'" -D /home/yashan/catalog
```

> **Note**: 
>
> If the deletion process is interrupted, it is possible that the backup set has been deleted from some nodes, while the remaining nodes still have the backup set and TAG. In this case, you can continue deleting by re-executing the delete command.
> 
> If there is backup set metadata in the catalog but no corresponding records in the database, deleting the backup set without specifying IF EXISTS may return an error. Please use IF EXISTS to delete the backup set. If the catalog file is accidentally deleted, or if the catalog and the target database backup set system table do not match, you can use this command with the addition of IF EXISTS to delete the system table information.
> 
> Only using this command can you simultaneously delete the backup set information from both the catalog and the system table.