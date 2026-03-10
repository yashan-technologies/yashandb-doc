The DBMS_DESCRIBE package provides a set of built-in stored procedures for obtaining parameter description information of user-defined stored procedures and is not applicable for ISC Distributed Cluster Deployment at this time.

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

The DESCRIBE_PROCEDURE procedure is used to obtain parameter description information for the specified user-defined stored procedure, with the output parameter types as follows:

- NUMBER_TABLE type: Represents a datatype of NUMBER type + index_datatype of INTEGER type [associative array](../PL Language Fundamentals/Variables/Collection Variables) (INDEX-BY TABLE).

- VARCHAR2_TABLE type: Represents a datatype of VARCHAR type + index_datatype of INTEGER type associative array.

|Parameter |Description |
| :-------- |:----------------------------------------|
| object_name | The name of the stored procedure, can use synonyms. Can be set to the name of a user-defined stored procedure (or function) or a sub-procedure (or sub-function) of a package. |
| reserved1 | Reserved field.                                 |
| reserved2 | Reserved field.                             |
| overload | Reserved field, returns 0.              |
| position | The position of the parameter in the parameter list. The position of the nth parameter is n, and the return value's position is 0.  |
| data_level | Reserved field, returns 0.     |
| argument_name | Parameter name.                           |
| datatype | Parameter data type ID. The corresponding data type name can be obtained by querying the V$DATATYPE view.             |
| default_value | Indicates whether the parameter has a default value; if so, it returns 1, otherwise it returns 0.                       |
| in_out | Parameter direction, 0 indicates IN, 1 indicates OUT, 2 indicates IN OUT.                 |
| length | Length of the data type of the parameter (character type width).               |
| precision | Precision of NUMBER type, returns 0 for non-NUMBER types.                |
| scale | Scale of NUMBER type, returns 0 for non-NUMBER types.                 |
| radix | Radix; TINYINT/SMALLINT/INTEGER/BIGINT/NUMBER types return 10, BIT type returns 2, other types return 0.                      |
| spare | Reserved field, returns 0.                        |
| include_string_constraints | For CHAR/VARCHAR/NCHAR/NVARCHAR/RAW type procedure parameters, when set to true, the length parameter returns character type width; when set to false, the length parameter returns 0. |

Usage Rules:

- The indexes of the returned associative array start from 1 and increase incrementally, with members ordered according to parameter positions.

- If there are no key-value pairs in the returned associative array, it indicates that the specified user-defined stored procedure has no parameters.

- If obtaining parameter information of a sub-procedure in a package returns an associative array containing only 1 key-value pair (argument_name is NULL, position is 1, and the rest are 0), it indicates that the sub-procedure has no parameters.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

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

## Exceptions

|Exception |Description |
| :---- | :---- |
| PROCEDURE_NOT_EXIST | The procedure does not exist and cannot be described. |
| PROCEDURE_INVALID | The procedure is invalid and cannot be described. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
-- Procedure does not exist
BEGIN
test_dbms_describe_proc('procabc');
EXCEPTION
WHEN DBMS_DESCRIBE.PROCEDURE_NOT_EXIST THEN
dbms_output.put_line('cannot describe a non-existent procedure');
END;
/
--result
cannot describe a non-existent procedure

-- Procedure is invalid
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
