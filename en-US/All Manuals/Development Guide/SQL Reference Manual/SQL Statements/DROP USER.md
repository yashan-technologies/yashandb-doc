General Description
----

DROP USER is used to delete an existing database user.

A user that is currently connected in a session cannot be deleted.

A user that has created any database objects cannot be deleted (unless CASCADE is specified).

In a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), deleting global users must be executed by connecting to the CDB root. 


Statement Definition
----

**drop user::=**

```ebnf+diagram
syntax::= DROP USER user_name [CASCADE]
```

### 1. user\_name

This statement is used to specify the name of the user to be deleted.

If the username contains special characters (except for underscores), such as `@`, `/`, `.`, `!`, `$`, `'`, etc., it must be surrounded by double quotes to ensure successful parsing.

### 2. CASCADE

This statement is used to specify that, when deleting a user, all objects under that user will also be deleted, including:

*   Tables: The table and all indexes and constraints on the table will be deleted; if there are foreign key references from a table (child table) of another user to this table, the foreign keys on the child table will be deleted synchronously.
*   Views, synonyms, PL objects: All will be deleted; if there are views, synonyms, or PL objects from another user that depend on this user's objects, those views, synonyms, or PL objects will not be deleted but will be marked as invalid.
*   Indexes: Will be deleted; if any indexes created under this user correspond to a table of another user, those indexes will also be deleted.
*   SEQUENCE and all other objects: All will be deleted.

***Example***

```sql
DROP USER sales1 CASCADE;
```
