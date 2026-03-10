**constraint::=**

```ebnf+diagram
syntax::= inline_constraint|out_of_line_constraint
```

**[inline\_constraint](#inlineconstraint)::=**

```ebnf+diagram
syntax::= [CONSTRAINT constraint_name] (UNIQUE|(PRIMARY KEY)|references_clause|CHECK condition|([NOT] NULL)) [constraint_state]
```

**[references\_clause](#referencesclause)::=**

```ebnf+diagram
syntax::= REFERENCES [schema "."] ref_table_name ( ["(" ((column_name) {"," (column_name)}) ")"] [ON DELETE (RESTRICT|NO ACTION|CASCADE|SET NULL)] [ON UPDATE (RESTRICT|CASCADE|SET NULL)])
```

**[constraint\_state](#constraintstate)::=**

```ebnf+diagram
syntax::= (NOT DEFERRABLE|INITIALLY IMMEDIATE|(RELY|NORELY)|using_index_clause|(ENABLE|DISABLE)|(VALIDATE|NOVALIDATE))
{" " (NOT DEFERRABLE|INITIALLY IMMEDIATE|(RELY|NORELY)|using_index_clause|(ENABLE|DISABLE)|(VALIDATE|NOVALIDATE))}
```

**[using\_index\_clause](#usingindexclause)::=**

```ebnf+diagram
syntax::= USING INDEX [(["schema."] index_name | "(" create_index_clause ")" | index_attr_clause )]
```

**[out\_of\_line\_constraint](#outlineconstraint)::=**

```ebnf+diagram
syntax::= [CONSTRAINT constraint_name] ((UNIQUE "(" ((column_name) {"," (column_name)}) ")")|(PRIMARY KEY "(" ((column_name) {"," (column_name)}) ")")|(FOREIGN KEY "(" ((column_name) {"," (column_name)}) ")" references_clause)|CHECK condition) [constraint_state]
```

constraint用于对表中的数据进行指定规则的约束定义，只有符合约束定义的数据才能被生成，否则系统将会提示违反约束错误。

约束基于列字段定义，被应用于CREATE TABLE、ALTER TABLE等与列字段属性定义相关的语法中。

根据在语句中位置不同，YashanDB提供了行内（inline_constraint）和行外（out_of_line_constraint）两种定义约束的方法，但NOT NULL约束项只能作为行内约束被定义。

<span id="inlineconstraint" name="inlineconstraint" class="yaslink"></span>

## inline\_constraint

行内约束，即在定义列字段属性的同时为其定义约束项。

可通过CONSTRAINT constraint_name指定约束项的名称，省略则由系统生成默认名称。

可以在行内定义的约束项有：

* [UNIQUE](#UNIQUE)：适用于所有表
* [PRIMARY KEY](#PRIMARYKEY)：适用于所有表
* [FOREIGN KEY](#REFERENCES)：适用于HEAP表
* [CHECK](#CHECK)：适用于HEAP表、TAC表
* [NOT NULL](#NOTNULL)：适用于所有表

示例（HEAP表）

```sql
-- 创建父表area_parent
CREATE TABLE area_parent
(area_no CHAR(2) NOT NULL PRIMARY KEY,
area_name VARCHAR2(60) CHECK (area_name IS NOT NULL),
DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
  
-- 创建子表branches_child，在area_no上建立到area_parent表的area_no字段上的外键约束，并定义当area_parent表的记录删除时，branches_child表受外键约束影响的行的area_no字段被置为空值
CREATE TABLE branches_child
(branch_no CHAR(4) PRIMARY KEY,
branch_name VARCHAR2(200) NOT NULL,
area_no CHAR(2) CONSTRAINT c_branches_child1 REFERENCES area_parent(area_no) ON DELETE SET NULL,
address VARCHAR2(200));
```

示例（TAC表）

```sql
DROP TABLE IF EXISTS area_tac;
CREATE TABLE area_tac
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60) CHECK (area_name IS NOT NULL),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
```

示例（LSC表）

```sql
CREATE TABLE area_lsc
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
```

<span id="outlineconstraint" name="outlineconstraint" class="yaslink"></span>

## out\_of\_line\_constraint

行外约束，即不在某一个列字段的属性定义中，而是独立定义。

可通过CONSTRAINT constraint_name指定约束项的名称，省略则由系统生成默认名称。

可以在行外定义的约束项有：

* [UNIQUE](#UNIQUE)：适用于所有表
* [PRIMARY KEY](#PRIMARYKEY)：适用于所有表
* [FOREIGN KEY](#REFERENCES)：适用于HEAP表
* [CHECK](#CHECK)：适用于HEAP表、TAC表

示例（HEAP表）

```sql
-- 创建子表branches_child1,并创建与行内约束中相同含义的外键约束
CREATE TABLE branches_child1
(branch_no CHAR(4) PRIMARY KEY,
branch_name VARCHAR2(200) NOT NULL,
area_no CHAR(2),
address VARCHAR2(200),
CONSTRAINT c_branches_child2 FOREIGN KEY (area_no) REFERENCES area_parent(area_no) ON DELETE SET NULL);
```

示例（TAC表）

```sql
CREATE TABLE area_tac1
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL,
 CONSTRAINT c_area_tac1 PRIMARY KEY(area_no),
 CONSTRAINT c_area_tac2 CHECK (area_name IS NOT NULL));
```

## 约束项

<span id="NOTNULL" name="NOTNULL" class="yaslink"></span>

### NOT NULL

为某个列字段定义非空约束项，表示要求该列字段上的值不能为空。

NULL或NOT NULL仅支持在行内约束中定义。

HEAP表不为空时不能新建NOT NULL约束的列。

NULL并不定义约束，只是显式允许列包含null。

如果既不指定NOT NULL也不指定NULL，则默认值为 NULL。

<span id="UNIQUE" name="UNIQUE" class="yaslink"></span>

### UNIQUE

为某个列字段或某些列字段组合定义唯一约束项，表示要求该列字段或列字段组合上的值在当前表中无重复。

如下为添加唯一约束项的限制：

- 当该列字段或列字段组合上已存在重复的数据时，无法成功添加此约束项。
- 唯一约束项添加成功后，系统将会自动创建一个基于唯一项字段的唯一索引；若该列字段或列字段组合上已存在索引（包括唯一索引和非唯一索引），则不会创建。
- 存算一体分布式集群部署中建Sharded表时，如果同时有多个唯一约束键，例如主键或唯一键，则限制所有的唯一约束键必须至少有一个公共字段 （默认的分区键取唯一约束键的公共子集中的第一个恰当的数据类型的字段），如不满足则返回错误。
- 存算一体分布式集群部署中在Sharded表中增加唯一性约束（包括主键约束和唯一约束），也必须包括分区键。

<span id="PRIMARYKEY" name="PRIMARYKEY" class="yaslink"></span>

### PRIMARY KEY

为某个列字段或某些列字段组合定义主键约束项，表示该列字段或列字段组合将作为表的主键。

由于行内约束是针对某一个列字段定义，因此在行内约束中只能定义单列主键，多列组合主键在行外约束中定义。

如下为添加主键约束项的限制：

- 主键拥有非空性特征，因此当该列字段或列字段组合中的任一列字段上存在空数据时，无法成功添加此约束项。
- 主键拥有唯一性特性，因此当该列字段或列字段组合上存在重复的数据时，无法成功添加此约束项。
- 一个表上只能有一个主键，因此当该列字段或列字段组合所在表已存在主键时，无法成功添加此约束项。
- 主键添加成功后，系统将会自动创建一个基于主键字段的唯一索引；若该列字段或列字段组合上已存在索引（包括唯一索引和非唯一索引），则不会创建。

<span id="CHECK" name="CHECK" class="yaslink"></span>

### CHECK

CHECK约束用于对要插入表的数据进行条件检查，只有条件返回为true时，数据才能被成功插入表中。

当对一个已存在数据的表添加新的CHECK约束项时，系统将对现有数据也执行CHECK条件检查（在指定了NOVALIDATE时不检查），当存在未通过检查的数据时，无法成功添加此约束项。

在行内约束中，CHECK的条件内容只能指定当前列字段，条件包含多列的CHECK在行外约束中定义。

CHECK的条件可以包含的内容描述请参考SQL通用语法[condition](./condition)。

<span id="REFERENCES" name="REFERENCES" class="yaslink"></span>

### FOREIGN KEY

为一个表（子表）的列字段或列字段组合建立到另一个表（父表）的外键约束，表示子表中该列字段或列字段组合上的值必须在父表对应的列上存在，若子表上的值为NULL，则认为在父表上存在对应值，符合外键约束。

由于行内约束是针对某一个列字段定义，因此在行内约束中只能定义单列外键，多列组合外键在行外约束中定义。

使用外键的约束如下：

- 不能为临时表建立FOREIGN KEY，也不能为其他表建立关联到临时表的FOREIGN KEY。
- 加密列不能创建FOREIGN KEY。

<span id="referencesclause" name="referencesclause" class="yaslink"></span>

#### references\_clause

在定义外键约束时，REFERENCES指定了外键指向的父表，及父表中与子表外键列一一对应的列字段。若仅标识父表名而省略列名，则外键会自动引用父表的主键并且外键列与主键列需要一一对应。

以下为添加外键约束项的限制：

- 外键约束要求子表中外键列上的数据在父表对应列上必须存在，在对一个已存在数据的子表添加新的外键约束项时，系统将对子表中的现有数据执行此项检查（在指定了NOVALIDATE时不检查），当存在未通过检查的数据时，无法成功添加此约束项。
- 外键约束要求父表对应列上的数据满足唯一性约束（该对应列上已定义PRIMARY KEY或UNIQUE约束项），否则无法成功添加此约束项。
- 当子表外键列到父表对应列的外键约束已经存在时，无法成功添加此约束项。
- 对于分区表，不允许添加外键级联约束on update cascade/on update set null。

##### ON DELETE 

在建立外键约束后，当对父表对应列上的某个值进行删除操作时，系统将检查该值在子表外键列上是否存在，如存在，该父表上的数据不允许删除。而在约束定义里指定了ON DELETE后，系统将允许前述对父表的操作，并同时根据不同选项执行如下操作：

- 指定ON DELETE RESTRICT（默认也是RESTRICT）创建外键后，对子表中存在数据的父表数据执行删除将报错。
- 指定ON DELETE CASCADE创建外键后，对子表中存在数据的父表数据执行删除将成功，且对子表中对应的数据也进行删除。
- 指定ON DELETE SET NULL创建外键后，对子表中存在数据的父表数据执行删除将成功，且对子表中对应的数据置空。

>  **Note**: 
>
> 子表需建立外键的列字段上存在NOT NULL约束项时，不能指定ON DELETE SET NULL选项创建外键。

此外，系统还提供如下仅用于语法兼容的选项：

- ON DELETE NO ACTION（行为与RESTRICT一致）

##### ON UPDATE

在建立外键约束后，当对父表对应列上的某个值进行更新操作时，系统将检查该值在子表外键列上是否存在，如存在，该父表上的数据不允许更新。而在约束定义里指定了ON UPDATE后，系统将允许前述对父表的操作，并同时根据不同选项执行如下操作：

- 指定ON UPDATE RESTRICT（默认也是RESTRICT）创建外键后，对子表中存在数据的父表数据执行更新将报错。
- 指定ON UPDATE CASCADE创建外键后，对子表中存在数据的父表数据执行更新将成功，且对子表中对应的数据也进行更新。
- 指定ON UPDATE SET NULL创建外键后，对子表中存在数据的父表数据执行更新将成功，且对子表中对应的数据置空。

> **Note**: 
>
> 子表需建立外键的列字段上存在NOT NULL约束项时，不能指定ON UPDATE SET NULL选项创建外键。

<span id="constraintstate" name="constraintstate" class="yaslink"></span>

## 约束项属性

YashanDB提供一系列选项用于定义某个约束项的属性，其中如下选项只用于语法兼容，并无实际含义：

- NOT DEFERRABLE
- INITIALLY IMMEDIATE
- RELY|NORELY

<span id="usingindexclause" name="usingindexclause" class="yaslink"></span>

### using\_index\_clause

该语句只作为PRIMARY KEY或UNIQUE约束项的属性选项使用，用于指定约束项对应的索引或索引属性，对其他约束项使用本语句将报错。

YashanDB中，创建PRIMARY KEY或UNIQUE约束项时，系统将自动创建一个对应的唯一索引（如该唯一索引已存在则不会创建）。使用本语句将系统自动的操作修改为人工指定，所实现效果略有差异，具体见下面描述。

#### USING INDEX

该语句仅用于已有约束项为disable的情况，将该约束项置为enable，并自动创建索引，索引名称等同约束名称。

#### USING INDEX [schema.]index\_name

为约束项指定一个已有的索引，该索引必须满足以下条件：

- 索引基于的表和列与约束项必须一致。
- 不能为倒序索引。

如果指定的索引不是唯一索引，则会在创建约束时将该索引重建为唯一索引。

通过此种方式指定的索引，与自动创建的索引的差别为：如后续对约束项进行删除操作，自动创建的索引会随之删除，而此种方式指定的索引并不会被删除。

示例（HEAP表）

```sql
-- 创建表area_using_index
CREATE TABLE area_using_index
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);

-- 创建唯一索引idx_area_using_index_1
CREATE UNIQUE INDEX idx_area_using_index_1 ON area_using_index(area_no);

-- 为表area_using_index创建主键并指定索引
ALTER TABLE area_using_index ADD PRIMARY KEY(area_no) USING INDEX idx_area_using_index_1;
```

#### USING INDEX index\_attr\_clause

使用本语句表示在创建约束项时，仍由系统自动创建唯一索引，但对该索引的相关属性进行指定。

在不使用using_index_clause且不存在唯一索引时，系统在创建主键时会自动创建一个唯一索引，该索引所有属性均采用默认值，例如索引的表空间将使用和表所在的表空间，使用本语句则可以避免单独构建索引的繁琐，同时也可以满足将索引和表进行物理隔离等实际需求。

需注意的是，使用本语句的前提是约束项列上不存在唯一索引，否则系统并不是直接采用该索引，而是返回失败。

**index_attr_clause**

指定索引属性的语句，其语法与[CREATE INDEX](../SQL语句/CREATE INDEX)中的index_attr_clause一致。

示例（HEAP表，单机TAC表）

```sql
-- 创建表area_index_attr，同时指定索引的表空间
CREATE TABLE area_index_attr
(area_no CHAR(2) PRIMARY KEY USING INDEX TABLESPACE yashan,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
```

#### USING INDEX create\_index\_clause

使用本语句等同于由系统自动创建唯一索引，但与系统自动创建采用默认值不同的是，本语句可以指定索引的名称、属性等各信息。

**create_index_clause**

创建索引的语句，除无法创建倒序索引之外，其余语法与[CREATE INDEX](../SQL语句/CREATE INDEX)一致。

需注意的是，当约束项列上已存在唯一索引时，create_index_clause中的index_name必须与已存在索引同名，且index_attr_clause所指定属性必须与已存在索引属性相同，否则返回失败。

示例（HEAP表、单机TAC表）

```sql
-- 创建表area_create_index
CREATE TABLE area_create_index
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);

-- 为表area_create_index创建主键并创建索引
ALTER TABLE area_create_index 
ADD PRIMARY KEY(area_no) 
USING INDEX (CREATE INDEX idx_area_create_index_1 ON area_create_index(area_no) TABLESPACE yashan);

-- 创建倒序索引会返回错误
ALTER TABLE area_create_index DROP PRIMARY KEY;
ALTER TABLE area_create_index 
ADD PRIMARY KEY(area_no) 
USING INDEX (CREATE INDEX idx_area_create_index_2 ON area_create_index(area_no DESC) TABLESPACE yashan);

YAS-02210 specified index cannot be used to enforce the constraint
```

###  VALIDATE|NOVALIDATE

指定在定义某个约束项时，是否对表中现有的数据进行该项约束检查，VALIDATE表示检查，NOVALIDATE表示不检查。可省略，则默认为VALIDATE。

对于UNIQUE/PRIMARY KEY/NOT NULL约束项，系统将始终默认为VALIDATE，执行约束检查，即使指定了NOVALIDATE。

示例（HEAP表、TAC表）

```sql
-- area表现有数据
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME                         DHQ         
------- --------------------------------- ------------
01      EastChina                            Shanghai      
02      WestChina                            Chengdu       
03      SouthChina                            Guangzhou     
04      NorthChina                            Beijing       
05      CentralChina                            Wuhan  

-- 为表添加CHECK约束
ALTER TABLE area ADD CHECK (LENGTH(area_name)>10);
YAS-02255 cannot validate constraint - check constraint violated

-- 为表添加NOVALIDATE的CHECK约束
ALTER TABLE area ADD CHECK (LENGTH(area_name)>10) NOVALIDATE;
```

### ENABLE|DISABLE

指定在定义某个约束项时，是否启用约束，ENABLE表示启用，DISABLE表示停用。可省略，则默认为ENABLE。

在定义UNIQUE/PRIMARY KEY约束项并指定DISABLE时，系统不会创建对应的索引。

示例（HEAP表、TAC表）

```sql
-- area表现有数据
SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME                         DHQ         
------- --------------------------------- ------------
01      EastChina                            Shanghai      
02      WestChina                            Chengdu       
03      SouthChina                            Guangzhou     
04      NorthChina                            Beijing       
05      CentralChina                            Wuhan  

-- 为表添加CHECK约束停用状态
ALTER TABLE area ADD CONSTRAINT ck CHECK (LENGTH(DHQ)>10) DISABLE;

-- 插入违反本约束的值，但需遵循上例中的CHECK约束
INSERT INTO area VALUES ('06','NorthChinaNorthChinaNorthChinaNorthChinaNorthChina1','tianjing');

-- 启用CHECK约束
ALTER TABLE area MODIFY CONSTRAINT ck ENABLE;
YAS-02255 cannot validate constraint - check constraint violated
```
