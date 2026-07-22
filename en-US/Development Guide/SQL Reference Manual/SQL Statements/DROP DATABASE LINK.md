General Description
----

The DROP DATABASE LINK statement is used to delete a database link object.

To delete a public database link, the user must have the DROP PUBLIC DATABASE LINK system privilege. To delete a private database link, the user must have the DROP DATABASE LINK system privilege.

Statement Definition
----

**[drop database link](#dropdatabaselink)::=**

```ebnf
= DROP [PUBLIC] DATABASE LINK dblink_name.
```

<span id="dropdatabaselink" name="dropdatabaselink"></span>

### PUBLIC

The keyword must be specified only when deleting a public database link.

***Example***

```sql
DROP PUBLIC DATABASE LINK dblink_yashan;
```

### dblink\_name

The name of the existing database link.

***Example***

```sql
-- Delete a normal database link
DROP DATABASE LINK dblink_yashan;

-- Delete a public database link
DROP PUBLIC DATABASE LINK dblink_oracle;
```
