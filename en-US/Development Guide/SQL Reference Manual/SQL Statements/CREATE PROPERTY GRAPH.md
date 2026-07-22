General Description
----

CREATE PROPERTY GRAPH creates a property graph object in the database. A property graph consists of vertices and edges, where each vertex and edge can have properties, similar to the Cypher graph query language model. Each vertex and edge in a property graph is stored separately in a table, and a property graph can contain at most 128 tables.

The created property graph can be used for graph queries through the [GRAPH_TABLE](./CREATE PROPERTY GRAPH.md#graphtablespec) function for pattern matching and traversal operations.

In ISC Distributed Cluster Deployment, users cannot execute this statement.

Statement Definition
----

**create property graph::=**

```ebnf
= CREATE PROPERTY GRAPH [IF NOT EXISTS] [schema "."] graph_name
    vertex_tables_clause
    [edge_tables_clause].
```

**vertex_tables_clause::=**

```ebnf
= VERTEX TABLES "(" (vertex_table_definition) {"," (vertex_table_definition)} ")".
```

**vertex_table_definition::=**

```ebnf
= graph_element_name_and_key [graph_table_label_and_properties].
```

**graph_element_name_and_key::=**

```ebnf
= graph_element_object_name [AS graph_element_name] [graph_element_key].
```

**graph_element_object_name::=**

```ebnf
= schema "." (table_name | view_name | synonym | dblink_name).
```

**graph_element_key::=**

```ebnf
= KEY "(" column_name_list ")".
```

**column_name_list::=**

```ebnf
= "(" column_name {"," column_name} ")".
```

**graph_table_label_and_properties::=**

```ebnf
= [graph_table_label_properties_clause] {LABEL label_identifier [PROPERTIES graph_table_properties_alternatives]}.
```

**graph_table_label_properties_clause::=**

```ebnf
= (NO PROPERTIES) | (PROPERTIES graph_table_properties_alternatives).
```

**graph_table_properties_alternatives::=**

```ebnf
= [ARE] ALL COLUMNS [EXCEPT "(" column_name_list ")"]
  | "(" column_or_expression {"," column_or_expression} ")".
```

**column_or_expression::=**

```ebnf
= (column_name [AS property_name]) | (value_expression AS property_name).
```

**graph_table_label_clause::=**

```ebnf
= ((LABEL label_identifier) | (DEFAULT LABEL)) [graph_table_label_properties_clause].
```

**edge_tables_clause::=**

```ebnf
= EDGE TABLES "(" (edge_table_definition) {"," (edge_table_definition)} ")".
```

**edge_table_definition::=**

```ebnf
= graph_element_name_and_key
    SOURCE vertex_table_reference
    DESTINATION vertex_table_reference
    [graph_table_label_and_properties].
```

**vertex_table_reference::=**

```ebnf
= graph_element_name | graph_element_key REFERENCES graph_element_name "(" column_name_list ")".
```

### IF NOT EXISTS

Specify IF NOT EXISTS to silently skip creation if the property graph already exists.

### graph_name

Specifies the name of the property graph to create. Required, must conform to YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

### VERTEX TABLES

Defines the list of vertex tables in the property graph. Each vertex table corresponds to a vertex type in the graph.

Vertex tables must specify unique identifier column(s) through KEY. The key column must be a primary key or unique index column.

#### graph_element_object_name

Specifies the database object (table, view, synonym, or dblink) corresponding to the vertex table, in the format `schema.object_name`.

#### AS

Specifies an alias for the graph element.

#### KEY

Specifies the key column(s) to uniquely identify a vertex.

#### LABEL

Specifies the label name for the vertex table, which must be unique within the same property graph. DEFAULT LABEL uses the table name as the default label.

In graph queries, the corresponding vertices will be referenced through the label name.

#### PROPERTIES

Specifies the property columns to map to graph elements:

- `PROPERTIES (column_list)`: Explicitly specify columns to map
- `PROPERTIES ARE ALL COLUMNS`: Map all columns of the table
- `PROPERTIES ARE ALL COLUMNS EXCEPT (column_list)`: Map all columns except specified ones
- `NO PROPERTIES`: Do not map any properties

### EDGE TABLES

Defines the list of edge tables in the property graph. Each edge table corresponds to an edge type in the graph.

Edge tables must clearly specify source and destination vertex reference relationships (SOURCE and DESTINATION).

#### SOURCE

Specifies the source vertex of the edge. The specification methods are as follows:

- Method 1: Directly specify the vertex table name
- Method 2: Specify the vertex table and its key column through KEY REFERENCES

#### DESTINATION

Specifies the destination vertex reference, same syntax as SOURCE.

#### KEY

Specifies the key column(s) to uniquely identify an edge.

#### LABEL

Specifies the label name for the edge table, which must be unique within the same property graph. DEFAULT LABEL indicates using the table name as the default label.

In graph queries, the corresponding edges will be referenced through the label name.

#### PROPERTIES

Specifies the property columns to map to edge elements, same as vertex tables.

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

```sql
-- Example 1: Create a simple social network property graph
CREATE TABLE person (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    age INT
);

CREATE TABLE friendship (
    id INT PRIMARY KEY,
    person_id INT,
    friend_id INT,
    since_date DATE
);

CREATE PROPERTY GRAPH social_network
    VERTEX TABLES (
        person LABEL person
    )
    EDGE TABLES (
        friendship
            KEY (id)
            SOURCE KEY (person_id) REFERENCES person (id)
            DESTINATION KEY (friend_id) REFERENCES person (id)
            LABEL friend
    );

-- Example 2: Create vertex labels with properties
CREATE PROPERTY GRAPH company_graph
    VERTEX TABLES (
        employees
            KEY (emp_id)
            LABEL employee
            PROPERTIES (emp_id, name, department),
        departments
            KEY (dept_id)
            LABEL department
            PROPERTIES ARE ALL COLUMNS
            EXCEPT (create_time)
    )
    EDGE TABLES (
        works_in
            KEY (id)
            SOURCE KEY (emp_id) REFERENCES employees (emp_id)
            DESTINATION KEY (dept_id) REFERENCES departments (dept_id)
            LABEL works_in
            PROPERTIES (id, role)
    );

-- Example 3: Multi-label property graph
CREATE PROPERTY GRAPH network_graph
    VERTEX TABLES (
        users
            KEY (user_id)
            LABEL user
            PROPERTIES (user_id, username, email),
        servers
            KEY (server_id)
            LABEL server
            PROPERTIES (server_id, hostname, ip_address)
    )
    EDGE TABLES (
        connections
            KEY (conn_id)
            SOURCE KEY (src_id) REFERENCES users (user_id)
            DESTINATION KEY (dst_id) REFERENCES users (user_id)
            LABEL connects
            PROPERTIES (bandwidth, latency)
    );

-- Example 4: Use IF NOT EXISTS
CREATE PROPERTY GRAPH IF NOT EXISTS social_network
    VERTEX TABLES (person)
    EDGE TABLES (
        friendship
            SOURCE KEY (person_id) REFERENCES person (id)
            DESTINATION KEY (friend_id) REFERENCES person (id)
    );
```