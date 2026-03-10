通用描述
----
CREATE ACCESS CONSTRAINT用于在指定的表上创建[AC对象](../../全部手册/开发手册/SQL参考手册/基本SQL元素/对象)。只能基于LSC表执行本语句创建AC对象，且该LSC表不可以为组合分区表或INTERVAL类型的分区表。

创建AC后，系统将在后台异步生成AC数据（运行[强制转换](ALTER TABLE.html#forcexfmrclause)可即时查看数据），AC数据可被用于：

- 执行源表查询时，在满足AC条件的情况下，系统将使用AC进行有界计算，加速查询。
- 用户可以像查询表一样直接查询AC，查询结果为当前后台已生成的AC数据。

当源表为一个分区表时，所创建的AC也将分区，且其分区界值、数量、分区表空间、存储属性等与源表一致。

语句定义
----

**create access constraint::=**

```ebnf+diagram
syntax::= CREATE ACCESS CONSTRAINT [schema "."] ac_name FROM [schema "."] table_name ac_model_clause
```

**[ac_model_clause](#acmodelclause)::=**

```ebnf+diagram
syntax::= ON xy_clause [n_clause] [where_clause] [aggr_clause][order_clause][ac_attr_clause]
```

**[xy_clause](#xyclause)::=**

```ebnf+diagram
syntax::= ( column_expr {( "," column_expr)} TO | ONLY ) column_expr  {( "," column_expr)}
```

**[column_expr](#columnexpr)::=**

```ebnf+diagram
syntax::= (column_name|column_oper_expr) [[AS] alias] 
```

**[column_oper_expr](#columnoperexpr)::=**

```ebnf+diagram
syntax::= (column_name|literal) ("+"|"-"|"*"|"/"|"%") (column_name|literal)
```

**[n_clause](#nclause)::=**

```ebnf+diagram
syntax::= BOUND literal
```

**[where_clause](#whereclause)::=**

```ebnf+diagram
syntax::= WHERE filter_clause [(AND|OR) filter_clause]
```

**[filter_clause](#flterclause)::=**

```ebnf+diagram
syntax::= ((column_name|literal) ("<"|"<="|">"|">="|"="|"!=") (column_name|literal))
| (column_name IS [NOT] NULL)
| (column_name IN "(" (literal) {"," (literal)} ")")
```

**[aggr_clause](#aggrclause)::=**

```ebnf+diagram
syntax::= INCLUDE aggr_func [[AS] alias] { "," aggr_func [[AS] alias]}
```

**[order_clause](#orderclause)::=**

```ebnf+diagram
syntax::= [NO] ORDER
```

**[ac_attr_clause](#acattrclause)::=**

```ebnf+diagram
syntax::= TABLESPACE (tablespace_name|DEFAULT)
```

### 1. ac\_name

该语句用于指定创建的AC的名称，不可省略，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

### 2. table\_name

该语句用于指定要创建AC的源表。创建AC的操作过程中会对该表加排他锁。

<span id="acmodelclause" name="acmodelclause" class="yaslink"></span>

### 3. ac\_model\_clause

用于建立AC数据模型的语句。

<span id="xyclause" name="xyclause" class="yaslink"></span>

#### 3.1. xy\_clause

该语句用于指定AC里的x值和y值，可通过TO关键字建立x to y的模型，也可以通过ONLY关键字建立只包含y值的模型。相同的x值和y值组合上只能创建一次AC对象，除非该对象被删除。

x/y值均由一个或多个基于列字段的表达式组成，以`,`分隔。

<span id="columnexpr" name="columnexpr" class="yaslink"></span>

##### 3.1.1. column\_expr

该语句用于指定x/y值基于列字段的表达式，表达式可以为：

- 要创建AC的表的列字段。
<span id="columnoperexpr" name="columnoperexpr" class="yaslink"></span>
- 列字段与常数，或者列字段与列字段的四则运算式。

表达式中必须至少包含一个列字段，但所有包含在表达式中的列字段均不能为LOB大对象型。

可以为每个表达式指定一个别名，若未指定，则使用默认命名。若别名与其他别名或者默认别名重复则报错。

示例（LSC表）

```sql
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_no TO area_name,dhq;
```

<span id="nclause" name="nclause" class="yaslink"></span>

#### 3.2. n\_clause

该语句用于指定AC的边界大小，BOUND为系统预留关键字，无实际含义，可省略，则默认为int64最大值。

如果指定本语句，需满足如下约束规则：

- BOUND的值必须为正整数，上限为int64最大值，即9223372036854775807。
- 只可以指定为一个常数。

<span id="whereclause" name="whereclause" class="yaslink"></span>

#### 3.3. where\_clause

该语句用于设置AC数据的过滤条件，可省略，则表示获取所有数据。

<span id="flterclause" name="flterclause" class="yaslink"></span>

##### 3.3.1. filter\_clause

可用于AC模型数据过滤的条件包括：

- =，!=，>，>=，<，<=：此类条件操作数必须为列字段与列字段，或者列字段与常数。
- IS NULL，IS NOT NULL：必须对列字段进行此类条件判断。
- IN：须对列字段进行此类条件判断，且IN后的集合只能包含常数。

示例（LSC表）

```sql
DROP ACCESS CONSTRAINT ac_area;
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_name TO area_no,dhq
WHERE dhq IN ('Shanghai','Chengdu')
AND area_name IS NOT NULL;
```

<span id="aggrclause" name="aggrclause" class="yaslink"></span>

#### 3.4. aggr\_clause

该语句用于指定AC列的聚集信息，可省略，则表示不对列进行聚集。

可同时指定多个聚集信息，以`,`分隔，但每个聚集信息只能针对一个列字段，可执行聚集的函数包括：

- SUM
- MAX
- MIN
- COUNT

上述聚集函数不可以进行嵌套，且不可以在单个AC中对相同列进行相同的聚集操作。

示例（LSC表）

```sql
DROP ACCESS CONSTRAINT ac_area;
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_name TO area_no,dhq
WHERE dhq IN ('Shanghai','Chengdu')
AND area_name IS NOT NULL
INCLUDE COUNT(dhq),MAX(area_no);
```

<span id="orderclause" name="orderclause" class="yaslink"></span>

#### 3.5. order\_clause

该语句用于指定AC的数据是否排序，省略则默认为ORDER。

<span id="acattrclause" name="acattrclause" class="yaslink"></span>

#### 3.6. ac\_attr\_clause

该语句用于指定AC的表空间，省略则默认为源表的表空间。存算一体分布式集群部署中用户无法为在分布表上创建的AC指定表空间，使用表所在的表空间作为AC的表空间。

*   tablespace_name：指定到一个存在的表空间。
*   DEFAULT：指定到缺省表空间，即源表的表空间。

在LSC表上创建的AC对象，其表空间必须指定为一个databucket表空间。

示例（单机LSC表）

```sql
DROP ACCESS CONSTRAINT ac_area;
CREATE ACCESS CONSTRAINT ac_area FROM area
ON area_name TO area_no,dhq
NO ORDER
TABLESPACE DEFAULT;
```
