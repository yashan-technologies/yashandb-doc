General Description
----

ALTER OUTLINE is used to modify a stored outline, including operations such as rebuilding, renaming, changing the ownership category, setting to valid, and setting to invalid.

Users must have the ALTER ANY OUTLINE privilege to modify a stored outline.

In an ISC Distributed Cluster Deployment, when executing this statement, the system will check for any unhandled DDL statements. If any exist, an error will be raised, and this statement must wait until the corresponding DDL is successfully recovered before it can be executed.

Statement Definition
----

**alter\_outline::=**

```ebnf+diagram
syntax::= ALTER [PUBLIC] OUTLINE outline_name (REBUILD|RENAME TO new_outline_name|CHANGE CATEGORY TO category_name|(ENABLE | DISABLE | SET ID UPGRADE))
```

### 1. PUBLIC

Public schema, default value.

### 2. outline\_name

The name of the OUTLINE to be modified.

### 3. REBUILD

Rebuild the OUTLINE, which generates the corresponding hint information again.

This syntax is not applicable to OUTLINE with SQL_ID syntax.

### 4. RENAME TO new\_outline\_name

Renames the OUTLINE. The new OUTLINE name cannot include a schema name and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

### 5. CHANGE CATEGORY TO category\_name

Changes the category of the OUTLINE. The category name can be either an existing one or a new one, cannot include a schema name, and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

This syntax is not applicable to OUTLINE with SQL_ID syntax.

### 6. ENABLE

Allows the use of this OUTLINE.

### 7. DISABLE

Disallows the use of this OUTLINE.

### 8. SET ID UPGRADE

This syntax is used only during the database upgrade process, executed automatically by the upgrade script, and cannot be used by the user.

***Example***

```sql
-- Rebuild the OUTLINE
ALTER OUTLINE ol_a REBUILD;

-- Change the ownership category of the OUTLINE, the system will automatically create nonexistent categories
ALTER OUTLINE ol_a CHANGE CATEGORY TO ctgy_new;

-- Disable the use of the OUTLINE
ALTER OUTLINE ol_a DISABLE;

-- Re-enable the OUTLINE
ALTER OUTLINE ol_a ENABLE;
```
