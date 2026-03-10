## General Description

DROP LIBRARY is used to delete an existing [user-defined library](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/User-Defined Librarys).

Regular users can only delete the user-defined libraries they have created. To delete another user's user-defined library, the user must have the DROP ANY LIBRARY privilege.

When the user-defined library to be deleted is referenced by an external UDF, deleting this user-defined library will result in an error at runtime for that external UDF.

## Statement Definition

**drop library::=**

```ebnf+diagram
syntax::= DROP LIBRARY [IF EXISTS] [schema "."] library_name
```

### 1. IF EXISTS

This clause is used to check whether the user-defined library exists before performing the DROP operation. If omitted, there will be no check, and if the user-defined library to be deleted does not exist, the system will return an error.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
DROP LIBRARY IF EXISTS ya_lib;
```
