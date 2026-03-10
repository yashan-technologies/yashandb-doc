During the use of the database, if database objects (such as tables, indexes, users, tablespaces, data files, etc.) are deleted, by default, the database service will not clean up the memory or storage area used by those objects. Therefore, residual data still exists in the corresponding memory and storage areas. When new resource usage requests occur, the corresponding resources can be directly allocated by the system, making it easy to steal residual data.

To protect residual data from being stolen, YashanDB cleans up residual data in the following ways:

* Clear the shared memory area of objects before releasing them across users.
* Clear extremely sensitive data, such as passwords, before releasing objects.
* Clear the corresponding file before deleting the disk file of the object.

In terms of product application, YashanDB controls whether to enable residual data protection functionality through system parameters. By default:

```sql
-- Check if EAL residual data protection functionality is enabled, default is FALSE and this functionality is not enabled.
show parameter enable_secure_erase;

name                                                             value                                                  
---------------------------------------------------------------- ----------------------------------------------------------------
ENABLE_SECURE_ERASE                                                      FALSE                                             


-- Modify system parameters to enable EAL residual data protection functionality, configuration takes effect immediately.
ALTER SYSTEM SET enable_secure_erase = true;


-- Check again if EAL residual data protection functionality is enabled.
show parameter enable_secure_erase;

name                                                             value                                                  
---------------------------------------------------------------- ----------------------------------------------------------------
ENABLE_SECURE_ERASE                                             TRUE                                                  

```

> **Note**:
> 
> After enabling the residual data protection functionality, if the object to be deleted is large, the deletion operation may respond slowly.