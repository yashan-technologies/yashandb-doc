```ebnf
from_vector =  FROM_VECTOR "(" expr [ RETURNING ( CLOB | VARCHAR  "(" size [BYTE | CHAR] ")" ) ] ")".

vector_serialize = VECTOR_SERIALIZE "(" expr [ RETURNING ( CLOB | VARCHAR  "(" size [BYTE | CHAR] ")" ) ] ")".

```
FROM_VECTOR函数用于将向量数据或向量形式的字符串数据转换为VARCHAR或者CLOB类型数据。

VECTOR_SERIALIZE函数与FROM_VECTOR函数含义相同。

**expr**

表示输入的向量或向量形式的字符串，字符串必须是一组由包裹在`[]`内的非空NUMBER，并且以`,`分割。

当expr的值为NULL时，函数返回NULL。

**RETURNING**

指定返回值的数据类型，可为VARCHAR或CLOB。

- 返回VARCHAR时指定长度和单位（BYTE或CHAR），省略BYTE或CHAR时表示为BYTE。
- 返回VARCHAR时，如果转换后的字符串长度超过指定长度，则报错。

示例（HEAP表）

```sql
CREATE TABLE table_convert_test(c1 int,c2 VECTOR(4,float32),c3 VECTOR(4,float64));
INSERT INTO table_convert_test VALUES(1,'[2.33,6.10,0.77,5.06]','[2.33,6.10,0.77,5.06]');
INSERT INTO table_convert_test VALUES(2,'','');
COMMIT;

SELECT FROM_VECTOR(c2 RETURNING varchar(66)) AS col FROM table_convert_test WHERE c1 = 1;

COL
----------------------------------------------------------------
[2.32999992E+000,6.0999999E+000,7.69999981E-001,5.05999994E+000]

SELECT FROM_VECTOR(c2 RETURNING varchar(0)) AS col FROM table_convert_test WHERE c1 = 2;

[1:41]YAS-04204 number of column size must be between 1 and 65534

SELECT FROM_VECTOR(c2 RETURNING varchar(65535)) AS col FROM table_convert_test;

[1:41]YAS-04204 number of column size must be between 1 and 65534

SELECT FROM_VECTOR(c2 RETURNING varchar(21)) AS col FROM table_convert_test WHERE c1 = 1;

YAS-04008 FROM_VECTOR() or VECTOR_SERIALIZE() RETURNING size exceeding limit 21

SELECT FROM_VECTOR(c3 RETURNING varchar(99)) AS col FROM table_convert_test WHERE c1 = 1;

COL
----------------------------------------------------------------
[2.3300000000000001E+000,6.0999999999999996E+000,7.7000000000000002E-001,5.0599999999999996E+000]

-- 返回值为CLOB

create or replace
  procedure P(c clob) is
  begin
    dbms_output.put_line(dbms_lob.getlength(c));
  end;
/

create table ctb(a clob, b int);
create table vtb(a vector);

variable my_lob clob;
exec :my_lob := '1,1';
exec for i in 1..16 loop :my_lob := :my_lob || :my_lob; end loop;
insert into ctb values(:my_lob, 1);

declare
  al clob;
  bl clob;
  cl clob;
begin
  al := '[';
  cl := ']';
  SELECT a into bl FROM ctb;
  dbms_lob.append(al,bl);
  dbms_lob.append(al,cl);
  insert into ctb values(al, 2);
end;
/

SELECT VECTOR_DIMENSION_COUNT(a) FROM vtb;
VECTOR_DIMENSION_COUNT(A)
-------------------------
                        6
                    32769
```
