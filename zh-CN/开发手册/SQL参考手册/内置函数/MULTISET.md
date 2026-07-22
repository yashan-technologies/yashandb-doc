```ebnf
multiset = CAST "(" MULTISET "(" subquery ")" AS type_name ")".
```

MULTISET函数将子查询subquery的结果集以集合（嵌套表）的形式返回，集合中的一个成员对应子查询返回的一行结果。MULTISET函数的返回值需要通过[CAST](CAST)函数转换为目标集合类型type_name。

**subquery**

指定的子查询。

**type\_name**

指定转换的目标集合类型，必须是可变长数组varray或嵌套表nested table，其中：

集合的成员类型是object，object中属性的个数和子查询投影列的个数相等，第n个投影列的类型能够转换为object中第n个属性的类型。

当投影列个数为1时，集合成员类型还可以是该投影列类型可转换的类型。

示例

```sql
create or replace type obj_multiset is object(col1 int, col2 int, col3 int);
/
create or replace type nt_multiset is table of obj_multiset;
/

select * from table(cast(multiset(select 1, 2, 3 from dual) as nt_multiset));

COL1         COL2         COL3 
------------ ------------ ------------ 
           1            2            3
```
