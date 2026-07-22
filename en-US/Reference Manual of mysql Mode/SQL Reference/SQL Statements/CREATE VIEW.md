## General Description

CREATE VIEW is used to create a view object.

Views with the same name can be created under different SCHEMAs.

## Statement Definition

**create view::=**

```ebnf
= CREATE [OR REPLACE] [ALGORITHM "=" {UNDEFINED | MERGE | TEMPTABLE}] [DEFINER "=" user] [SQL SECURITY { DEFINER | INVOKER }] VIEW [schema "."] view_name AS select_statement [WITH [CASCADED | LOCAL] CHECK OPTION].
```

### ALGORITHM

Provides syntax compatibility only, with no actual effect on behavior.

### DEFINER

Supports both username and username@hostname syntax forms.

- username supports syntax parsing of CURRENT_USER and CURRENT_USER() keywords and identifies the user as the current session user; supports regular names, which follow yashan naming rules and can be enclosed in backticks.

- hostname supports syntax parsing, including % and regular names, with regular names following yashan naming rules.

### SQL SECURITY

When querying a view, the objects (tables, views, etc.) referenced in the view will be queried. This statement is used to define the security context used when executing the view:

- DEFINER: When querying the view, the querying user only needs permission to access the view, but the definer must have access permissions to the objects referenced by the view.

- INVOKER: When querying the view, the querying user needs permission to access the view and also needs access permissions to the objects referenced by the view.

When SQL SECURITY is not specified, the default is DEFINER.

### WITH CHECK OPTION

Provides syntax compatibility only, with no actual effect on behavior.


***Example*** for Heap tables

```sql
mysql> create or replace algorithm = merge definer = user1 sql security definer view view_test1 as select * from example1.area with local check option;
Query OK, 0 rows affected (0.03 sec)
```