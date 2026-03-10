General Description
----

CREATE SYNONYM is used to create a synonym object for a database object. Users can use the synonym in SQL statements to replace the original object.

In ISC Distributed Cluster Deployment, users cannot execute this statement.

Statement Definition
----

**CREATE SYNONYM::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] [EDITIONABLE|NONEDITIONABLE] [PUBLIC] SYNONYM [schema "."] synonym FOR ([schema "."] object | object"@"database_link_object)
```

### 1. OR REPLACE

This clause is used to specify that if the synonym to be created already exists, it will be rebuilt.

### 2. PUBLIC

This clause is used to specify that the synonym is a public synonym, meaning all users can access this synonym. In this case, the schema owner cannot be specified for the synonym.

This clause is optional; if omitted, it defaults to a private synonym, which adheres to the schema statement description rules.

### 3. EDITIONABLE | NONEDITIONABLE

This is used for syntax compatibility and has no actual meaning.

### 4. schema

This clause is used to specify the owner of the synonym, i.e., the username. It is optional; if omitted, the default owner is the current user.

### 5. synonym

This clause is used to specify the name of the synonym to be created. It is mandatory and must comply with YashanDB's [Object Naming Conventions](../Basic SQL Elements/Identifiers).

### 6. object

This clause is used to specify the name of the object for which a synonym will be created. At this time, there will be no check to see if the object exists or is valid; an error will only occur when using this synonym.

### 7. object@database\_link\_object

This clause is used to specify the object for which a synonym will be created, derived from a dblink connection. Currently, the object supports table and view types through dblink connections. At this time, there will be no check to see if the object exists or is valid; an error will only occur when using this synonym.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Create a public synonym for the current user's object
CREATE PUBLIC SYNONYM sy_area1 FOR area;
 
-- Create a private synonym for another user's object. Even if the area table does not exist, it can still be created successfully.
CREATE SYNONYM sy_area2 FOR sales.area;
```
