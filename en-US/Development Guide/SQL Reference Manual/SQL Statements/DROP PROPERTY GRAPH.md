General Description
----

DROP PROPERTY GRAPH is used to delete an existing property graph object from the database.

In ISC Distributed Cluster Deployment, users cannot execute this statement.

Statement Definition
----

**drop property graph::=**

```ebnf
= DROP PROPERTY GRAPH [schema "."] graph_name [IF EXISTS].
```

### IF EXISTS

Specify IF EXISTS to silently skip deletion if the property graph does not exist.

### graph_name

Specifies the name of the property graph to delete.

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

```sql
-- Example 1: Drop property graph
DROP PROPERTY GRAPH social_network;

-- Example 2: Skip without error if property graph does not exist (using IF EXISTS)
DROP PROPERTY GRAPH IF EXISTS non_existent_graph;

-- Example 3: Drop property graph with schema
DROP PROPERTY GRAPH myschema.my_graph;
```