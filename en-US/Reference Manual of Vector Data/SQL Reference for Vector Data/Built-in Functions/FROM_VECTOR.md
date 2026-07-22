```ebnf
from_vector =  FROM_VECTOR "(" expr [ RETURNING ( CLOB | VARCHAR  "(" size [BYTE | CHAR] ")" ) ] ")".

vector_serialize = VECTOR_SERIALIZE "(" expr [ RETURNING ( CLOB | VARCHAR  "(" size [BYTE | CHAR] ")" ) ] ")".

```
The FROM_VECTOR function is used to convert vector data or string data in vector form to VARCHAR or CLOB type data.

The VECTOR_SERIALIZE function has the same meaning as the FROM_VECTOR function.

**expr**

Indicates the input vector or string in vector form. The string must be a set of non-empty NUMBER values wrapped in `[]` and separated by `,`.

When the value of expr is NULL, the function returns NULL.

**RETURNING**

Specify the data type of the return value, which can be VARCHAR or CLOB.

- When returning VARCHAR, you can specify the length and unit (BYTE or CHAR). If the unit is not specified, it defaults to BYTE.
- When returning VARCHAR, if the converted string length exceeds the specified length, an error is reported.

***Example*** for Standalone Deployment Heap tables

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

-- Return a CLOB data

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
