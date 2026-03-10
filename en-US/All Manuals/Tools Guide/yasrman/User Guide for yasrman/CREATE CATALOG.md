## Introduction to Catalog

*yasrman* uses the catalog to store parameters and backup set metadata. The catalog is a folder that contains the following contents:

- backup: The default backup set directory for storing the backup sets specified to be stored on the tool side (DEST CLIENT). When a backup is performed without specifying an absolute path, the backup sets are stored in this directory by default.

- catalog.meta: A metadata file that contains the positions of backup sets, TAGs, and relevant metadata for distributed nodes, and it is a binary file.

- config.ini: A configuration file used to store configuration information for backup and recovery statements.

- catalog.dwf: Internal management control file.

When using *yasrman* for the first time, it is necessary to create a catalog first.

## Creating the Catalog

CREATE CATALOG is used to create a new catalog path.

```ebnf+diagram
syntax::= CREATE CATALOG
```

This command will create a catalog folder based on the path and name specified by the -D option. If not specified, a folder named "catalog" will be created in the current path. If the folder already exists, this command will return an error.

> **Note**: 
>
> - The catalog folder contains binary files that record backup set metadata information and should not be deleted manually. Deleting or changing the catalog path may lead to exceptions such as being unable to use *yasrman* to restore the contained backup sets or to view backup set information.
> - The catalog is not bound to any specific database, but it is recommended to create independent catalog files for different databases.
> - *yasrman* does not back up or restore the catalog. Users can manually copy the catalog directory for backup when *yasrman* is not in use.
> - The metadata information of the catalog backup set will not automatically synchronize with the metadata information in the database system tables. If operations such as catalog migration or catalog reconstruction have occurred, it may result in the [YAS-02505](../../../Reference Manual/Error Codes) error indicating that the specified TAG for the backup already exists (i.e., conflict with the TAG in the database system table metadata). For cleaning up conflicting TAGs, refer to [Delete Backup Sets](DELETE).

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'create catalog' -D /home/yashan/catalog
```
