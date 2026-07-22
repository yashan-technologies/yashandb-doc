通用描述
----

CREATE PROPERTY GRAPH用于在数据库中创建一个属性图（Property Graph）对象。属性图由顶点和边组成，每个顶点和边都可以拥有属性，类似于Cypher图查询语言的图模型。属性图的每个顶点和边单独存储在一个表中，每个属性图最多包含128个表。

创建的属性图可以用于图查询，通过[GRAPH_TABLE](CREATE PROPERTY GRAPH.md#graphtablespec)函数在图上进行模式匹配和遍历操作。

存算一体分布式集群部署中用户无法执行该语句。

语句定义
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
= graph_element_object_name [AS graph_element_name ] [graph_element_key].
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

指定IF NOT EXISTS表示如果属性图已存在，不报错，静默跳过创建操作。

### graph_name

指定要创建的属性图的名称，不可省略，需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

### VERTEX TABLES

定义属性图中的顶点表清单。每个顶点表对应图中的一个顶点类型。

顶点表必须通过KEY指定唯一标识列，且KEY指定的列必须是表的主键或唯一索引列。

#### graph_element_object_name

指定顶点表对应的数据库对象（表、视图、同义词或dblink），格式为`schema.object_name`。

#### AS

为图元素指定别名。

#### KEY

指定表的键列，用于唯一标识顶点。

#### LABEL

指定顶点表的标签名称，须在同一属性图内唯一。DEFAULT LABEL表示使用表名作为默认标签。

在图查询中将通过标签名称引用相应的顶点。

#### PROPERTIES

指定映射到图元素的属性列：

- `PROPERTIES (column_list)`：显式指定要映射的列
- `PROPERTIES ARE ALL COLUMNS`：映射表的所有列
- `PROPERTIES ARE ALL COLUMNS EXCEPT (column_list)`：映射所有列，但排除指定列
- `NO PROPERTIES`：不映射任何属性

### EDGE TABLES

定义属性图中的边表清单。每个边表对应图中的一条边类型。

边表必须明确指定源顶点和目标顶点的引用关系（SOURCE和DESTINATION）。

#### SOURCE

指定边的源顶点，指定方式如下：

- 方式1：直接指定顶点表名
- 方式2：通过KEY REFERENCES指定顶点表及其键列

#### DESTINATION

指定边的目标顶点，语法同SOURCE。

#### KEY

指定边表的键列，用于唯一标识边。

#### LABEL

指定边表的标签名称，须在同一属性图内唯一。DEFAULT LABEL表示使用表名作为默认标签。

在图查询中将通过标签名称引用相应的边。

#### PROPERTIES

指定映射到边元素的属性列，用法同顶点表。

示例（单机/共享集群/分布式集群部署）

```sql
-- 示例1：创建简单的社交网络属性图
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

-- 示例2：创建带属性的顶点标签
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

-- 示例3：多标签属性图
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

-- 示例4：使用IF NOT EXISTS
CREATE PROPERTY GRAPH IF NOT EXISTS social_network
    VERTEX TABLES (person)
    EDGE TABLES (
        friendship
            SOURCE KEY (person_id) REFERENCES person (id)
            DESTINATION KEY (friend_id) REFERENCES person (id)
    );
```