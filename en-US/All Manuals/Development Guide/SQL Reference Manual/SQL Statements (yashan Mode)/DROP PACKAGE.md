General Description
----

The DROP PACKAGE command is used to delete an existing [package](../../PL参考手册/PL对象/自定义高级包).

Ordinary users can only delete the advanced packages they have created.

If the variables or sub-objects of the advanced package to be deleted are referenced by other procedure bodies, deleting this advanced package will result in a compilation error when those procedure bodies are run.

Statement Definition
----

**drop package::=**

```ebnf+diagram
syntax::= DROP PACKAGE [BODY] [IF EXISTS] [schema "."] package_name
```

### 1. BODY

This is used to specify whether to delete only the BODY of the package. If BODY is not specified, both the PACKAGE HEAD and PACKAGE BODY will be deleted.

### 2. IF EXISTS

This clause is used to specify whether to check for the existence of the package before deletion. If omitted, there will be no check; if the package to be deleted does not exist, the system will return an error.

***Example*** for Standalone Deployment and YAC Deployment

```sql
DROP PACKAGE IF EXISTS calc_fee;
```
