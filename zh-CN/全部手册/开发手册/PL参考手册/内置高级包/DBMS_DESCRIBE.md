DBMS_DESCRIBE包提供了一组内置的存储过程，用于获取用户自定义存储过程的参数描述信息，暂不适用于存算一体分布式集群部署。

## DESCRIBE\_PROCEDURE

```plsql
DBMS_DESCRIBE.DESCRIBE_PROCEDURE(
   object_name                   IN  VARCHAR,
   reserved1                     IN  VARCHAR,
   reserved2                     IN  VARCHAR,
   overload                      OUT NUMBER_TABLE,
   position                      OUT NUMBER_TABLE,
   data_level                    OUT NUMBER_TABLE,
   argument_name                 OUT VARCHAR2_TABLE,
   datatype                      OUT NUMBER_TABLE,
   default_value                 OUT NUMBER_TABLE,
   in_out                        OUT NUMBER_TABLE,
   length                        OUT NUMBER_TABLE,
   precision                     OUT NUMBER_TABLE,
   scale                         OUT NUMBER_TABLE,
   radix                         OUT NUMBER_TABLE,
   spare                         OUT NUMBER_TABLE,
   include_string_constraints    IN  BOOLEAN DEFAULT FALSE); 
```

DESCRIBE_PROCEDURE程序用于获取指定用户自定义存储过程的参数描述信息，输出参数的类型如下：

- NUMBER_TABLE类型：表示datatype为NUMBER类型+index_datatype为INTEGER类型的[关联数组](../PL语言基础/变量/集合变量)（INDEX-BY TABLE）。

- VARCHAR2_TABLE类型：表示datatype为VARCHAR类型+index_datatype为INTEGER类型的关联数组。

|  参数| 描述|
| :-------- |:----------------------------------------|
| object_name | 存储过程名，可以使用同义词。可设置为用户自定义存储过程（或函数）名或用户自定义包的子过程（或子函数）名。             |
| reserved1 | 保留字段。                                 |
| reserved2 | 保留字段。                             |
| overload | 保留字段，返回0。              |
| position | 参数在参数列表中的位置。第n个参数的位置为n，返回值的位置为0。  |
| data_level | 保留字段，返回0。     |
| argument_name | 参数名。                           |
| datatype | 参数的数据类型ID。可以通过查询V$DATATYPE视图获取对应的数据类型名。             |
| default_value | 参数是否具有默认值，是则返回1，否则返回0。                       |
| in_out | 参数方向，0表示IN方向，1表示OUT方向，2表示IN OUT方向。                 |
| length | 参数的数据类型长度（字符类型宽度）。               |
| precision | NUMBER类型精度，非NUMBER类型返回0。                |
| scale | NUMBER类型刻度，非NUMBER类型返回0。                 |
| radix | 基数，TINYINT/SMALLINT/INTEGER/BIGINT/NUMBER类型返回10，BIT类型返回2，其他类型返回0。                      |
| spare | 保留字段，返回0。                        |
| include_string_constraints | 对于CHAR/VARCHAR/NCHAR/NVARCHAR/RAW类型的过程体参数，置为true时length参数返回字符类型宽度，置为false时length参数返回0。 |

程序使用规则：

- 返回的关联数组下标从1开始递增，成员的顺序和参数位置的顺序一致。

- 若返回的关联数组中不存在键值对成员，则表示指定的用户自定义存储过程无参数。

- 若在获取用户自定义包中的子过程的参数信息时，返回的关联数组只包含1个键值对成员（argument_name为NULL，position为1，其余为0），则表示该子过程无参数。

示例（单机、共享集群）

```plsql
CREATE OR replace PROCEDURE test_dbms_describe_proc(obj VARCHAR, getstrlen BOOLEAN DEFAULT false) IS
overload  dbms_describe.number_table;
position1 dbms_describe.number_table;
c_level   dbms_describe.number_table;
arg_name  dbms_describe.varchar2_table;
dty       dbms_describe.number_table;
def_val   dbms_describe.number_table;
p_mode    dbms_describe.number_table;
length1   dbms_describe.number_table;
precision dbms_describe.number_table;
scale     dbms_describe.number_table;
radix     dbms_describe.number_table;
spare     dbms_describe.number_table;
BEGIN
dbms_describe.describe_procedure(
obj,null,null,overload,position1,c_level,
arg_name,dty,def_val,p_mode,length1,
precision,scale,radix,spare,getstrlen);
dbms_output.put_line('arg count: '||arg_name.count);
FOR i IN arg_name.first..arg_name.last LOOP
dbms_output.put_line('overload: '||overload(i));
dbms_output.put_line('position: '||position1(i));
dbms_output.put_line('level: '||c_level(i));
dbms_output.put_line('arg_name: '||arg_name(i));
dbms_output.put_line('datatype: '||dty(i));
dbms_output.put_line('default: '||def_val(i));
dbms_output.put_line('in_out: '||p_mode(i));
dbms_output.put_line('length: '||length1(i));
dbms_output.put_line('precision: '||precision(i));
dbms_output.put_line('scale: '||scale(i));
dbms_output.put_line('radix: '||radix(i));
dbms_output.put_line('spare: '||spare(i));
END LOOP;
END;
/

exec test_dbms_describe_proc('test_dbms_describe_proc');

--result
arg count: 2
overload: 0
position: 1
level: 0
arg_name: OBJ
datatype: 26
default: 0
in_out: 0
length: 0
precision: 0
scale: 0
radix: 0
spare: 0
overload: 0
position: 2
level: 0
arg_name: GETSTRLEN
datatype: 1
default: 1
in_out: 0
length: 1
precision: 0
scale: 0
radix: 0
spare: 0
```

## 异常

|  异常| 描述|
| :---- | :---- |
| PROCEDURE_NOT_EXIST | 过程不存在，无法描述。 |
| PROCEDURE_INVALID | 过程失效，无法描述。 |

示例（单机、共享集群）

```plsql
-- 过程不存在
BEGIN
test_dbms_describe_proc('procabc');
EXCEPTION
WHEN DBMS_DESCRIBE.PROCEDURE_NOT_EXIST THEN
dbms_output.put_line('cannot describe a non-existent procedure');
END;
/
--result
cannot describe a non-existent procedure

-- 过程失效
CREATE OR replace PROCEDURE procabc IS
BEGIN
a:=1;
END;
/

YAS-04253 PL/SQL compiling errors:
[3:1] YAS-04243 invalid identifier "A"

BEGIN
test_dbms_describe_proc('procabc');
EXCEPTION
WHEN DBMS_DESCRIBE.PROCEDURE_INVALID THEN
dbms_output.put_line('cannot describe an invalid procedure');
END;
/
--result
cannot describe an invalid procedure
```
