The VALIDATE command is used to check if the backup set files recorded in the catalog are complete, thereby determining whether they can be used for recovery.

```ebnf+diagram
syntax::= VALIDATE BACKUPSET (TAG "tag_name" | "backupset_path")
```

You can specify a TAG for the backup set to validate whether the TAG exists in the catalog and is accessible locally.

Alternatively, you can specify the absolute path of the backup set directly to confirm if the path is locally accessible.

***Example***

```shell
$ yasrman -c "VALIDATE BACKUPSET tag 'full_1'" -D /home/yashan/catalog

$ yasrman -c "VALIDATE BACKUPSET '/home/yashan/full_1'" -D /home/yashan/catalog

$ yasrman -c "VALIDATE BACKUPSET '+DG0/bak1'" -D /home/yashan/catalog
```

> **Note**: 
>
> The validation command must specify the catalog path.
>
> The validation command does not need to establish a connection to the database, so IP information does not need to be specified.
>
> If you need to validate backup set files in shared storage, you need to import the environment variables YASCS_HOME and YASFS_HOME before execution, or you will not have access to shared storage files.