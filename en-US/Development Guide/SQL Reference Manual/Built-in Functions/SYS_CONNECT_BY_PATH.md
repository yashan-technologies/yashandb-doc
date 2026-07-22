```ebnf
SYS_CONNECT_BY_PATH = SYS_CONNECT_BY_PATH "(" colName "," delimiter ")".
```

SYS_CONNECT_BY_PATH is a function used in hierarchical query scenarios. This function returns the values of all nodes named colName along the path from the root node to the current node, separated by the specified character delimiter.

The return type of this function is VARCHAR, with a maximum return length of 32768. Exceeding this length will result in an error.

This function does not support vectorization calculation.

**colName**

This parameter is the column name of the table in the statement.

**delimiter**

A character or string separator. It can only be a constant or a constant string.

***Example*** for Heap tables

```sql
drop table if exists area_info;
create table area_info (id int, area_name varchar(10), father_id int);
insert into area_info values(2, 'Zhejiang', 0);
insert into area_info values(571, 'Hangzhou', 2);
insert into area_info values(1, 'Guangdong', 0);
insert into area_info values(755, 'Shenzhen', 1);
insert into area_info values(756, 'Longhua', 755);
insert into area_info values(757, 'Futian', 755);

SELECT  id, father_id, LEVEL,
CONNECT_BY_ROOT area_name as name, 
SYS_CONNECT_BY_PATH(area_name, '——') path    
FROM area_info  
CONNECT BY PRIOR id = father_id START WITH father_id = 0  
ORDER SIBLINGS BY id DESC;  
      ID    FATHER_ID         LEVEL NAME          PATH
-------- ------------ ------------- ------------- --------------------------------------
       2            0             1 Zhejiang      ——Zhejiang
     571            2             2 Zhejiang      ——Zhejiang——Hangzhou
       1            0             1 Guangdong     ——Guangdong
     755            1             2 Guangdong     ——Guangdong——Shenzhen
     757          755             3 Guangdong     ——Guangdong——Shenzhen——Futian
     756          755             3 Guangdong     ——Guangdong——Shenzhen——Longhua
```
