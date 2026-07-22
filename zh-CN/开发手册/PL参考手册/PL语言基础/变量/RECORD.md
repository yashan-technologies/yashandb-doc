在声明RECORD类型之前，需要先进行RECORD类型的结构定义，包括用户自定义和系统隐形定义：

- 用户自定义：通过TYPE ... IS RECORD语法显式定义REORD的结构成员信息。
- %TYPE：将某个变量声明为[引用变量](./引用变量)%TYPE类型时，如被引用的变量为一个RECORD或游标，则系统自动继承被引用变量的结构定义，声明的变量为一个RECORD集合变量。
- %ROWTYPE：将某个变量声明为[引用变量](./引用变量)%ROWTYPE类型时，系统自动继承被引用对象的行结构定义，声明的变量为一个RECORD集合变量。

## 用户自定义RECORD

用户自定义RECORD需要显式指定RECORD的类型名称、成员名称、成员数据类型信息。其中数据类型不能定义为游标类型。

定义语法如下：

```ebnf
= TYPE record_type IS RECORD "(" member_name (dataType|recodeType) [[NOT] NULL] [(":="|DEFAULT) default_value]
{"," member_name (dataType|recodeType) [[NOT] NULL] [(":="|DEFAULT) default_value]} ")" ";".
```

RECORD变量声明语法如下：

```ebnf
= variable_name record_type [[NOT] NULL]";".
```

**dataType**

PL中定义的[用户自定义类型](../../PL对象/自定义类型)和SQL中定义的[普通标量数据类型](../../../SQL参考手册/数据类型/00数据类型)。当数据类型为字符型时，须同时指定其Size属性，例如VARCHAR(10)，但输入CHAR默认等同于CHAR(1)；可指定的最大Size为65534。 

**variableName**

要创建的RECORD的名称，不可省略，且需符合YashanDB的[对象命名规范](../../../SQL参考手册/基本SQL元素/标识符)。

- 在定义`rowid`为RECORD类型时，定义前使用`rowid`进行声明的变量为内置类型，定义后使用`rowid`进行声明的变量为RECORD类型。

**recodeType**

在一个RECORD结构中嵌套另一个RECORD，YashanDB支持最多16层嵌套（当用户自定义RECORD前使用了Label标签时，RECORD最多嵌套15层）。嵌套的RECORD不能定义为引用游标%TYPE类型。

**default_value**

为成员变量定义一个默认值。

## RECORD赋值

### 通过成员变量赋值

通过"."操作符访问RECORD变量的member成员，对成员分别赋值，或通过对象初始化方式对成员批量赋值。

示例

```plsql
DECLARE
TYPE name IS RECORD (
first VARCHAR(20),
last VARCHAR(20) := 'Smith'
);
name1 name;
name2 name;
name3 name;
BEGIN
name1.first := 'Jane'; 
name1.last := 'Smith';
DBMS_OUTPUT.PUT_LINE('name1: ' || name1.first || ' ' || name1.last);
DBMS_OUTPUT.PUT_LINE('name2: ' || name2.first || ' ' || name2.last);
--初始化方式
name3 := name('Lisa','Stone');
DBMS_OUTPUT.PUT_LINE('name3: ' || name3.first || ' ' || name3.last);
END;
/

--result
name1: Jane Smith
name2:  Smith
name3: Lisa Stone
```

### RECORD变量间赋值

两个RECORD变量间的赋值规则为：

- 源和目标均为用户自定义的RECORD类型时：只能在声明为同一个recode_type的变量间赋值。
- 源和目标其中一个为系统隐形定义的RECORD类型时，只有两边的成员数量和数据类型均一致时，才可以赋值。其中数据类型一致的含义为：
  - 显式的一致，即系统不进行隐式的数据类型转换。
  - 不要求精度、长度等属性一致。

示例

```plsql
--如下不能赋值
DECLARE
TYPE name_1 IS RECORD (
first VARCHAR(20),
last VARCHAR(20) := 'Smith'
);
TYPE name_2 IS RECORD (
first VARCHAR(20),
last VARCHAR(20) := 'Smith'
);
name1 name_1;
name2 name_2;
BEGIN
name1 := name2;
END;
/
YAS-04253 PL/SQL compiling errors:
[13:10] YAS-00014 illegal conversion from NAME_2 to NAME_1

CREATE TABLE name_1 (c1 NUMBER, c2 CHAR(4));
--如下可以赋值
DECLARE
TYPE name_1 IS RECORD (
first NUMBER(6),
last VARCHAR(40)
);
name1 name_1;
name2 name_1%ROWTYPE;
BEGIN
name1 := name2;
END;
/
--如下不能赋值
DECLARE
TYPE name_1 IS RECORD (
first INT,
last CHAR(4)
);
name1 name_1;
name2 name_1%ROWTYPE;
BEGIN
name1 := name2;
END;
/
YAS-04253 PL/SQL compiling errors:
[9:10] YAS-05286 illegal conversion
```

### 通过SQL语句赋值

通过SQL语句执行RECORD变量的赋值操作（作为源或目标）时，要求源和目标的结构成员一致，且数据类型兼容，即能满足系统的隐式数据类型转换。

#### INSERT语句

通过INSERT INTO语句将一个RECORD变量插入到表中，此时只能对表的所有列项整体插入，不能分开列项插入。

示例

```plsql
DECLARE
TYPE area_type IS RECORD (
area_no VARCHAR(5),
area_name VARCHAR(10),
dhq VARCHAR(5)
);
a area_type;
BEGIN
a.area_no := '09';
a.area_name := 'example';
a.dhq := 'exa';
INSERT INTO area VALUES a;
END;
/
```

#### SELECT语句

通过SELECT...INTO语句将查询结果赋值给一个RECORD变量，此时只能将SELECT后的列项整体赋给一个RECORD变量，不能分开赋值，且查询结果必须为单行记录。

示例

```plsql
DECLARE
TYPE area_type IS RECORD (
area_no NUMBER,
area_name VARCHAR(10)
);
a area_type;
BEGIN
SELECT area_no,area_name INTO a
FROM area 
WHERE area_no = '09';
DBMS_OUTPUT.PUT_LINE(a.area_no || ',' || a.area_name);
END;
/

--result
9,example
```
