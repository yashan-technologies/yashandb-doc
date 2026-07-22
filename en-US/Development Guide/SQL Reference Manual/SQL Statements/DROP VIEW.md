General Description
----

DROP VIEW is used to delete a view, including views that are in an invalid state.

Statement Definition
----

**drop view::=**

```ebnf
= DROP VIEW [IF EXISTS] [ schema '.' ] view_name.
```

### IF EXISTS

This clause specifies that before dropping the view, the system should check if the view exists. If the view does not exist, the operation returns success. If omitted, the system will raise an error if the view to be deleted does not exist.

### schema

The schema name to which the target view belongs. If omitted, the default is the schema of the currently logged-in user.

### view\_name

This clause is used to specify the name of the view to be deleted.

***Example***

```sql
DROP VIEW IF EXISTS v_area;
```
