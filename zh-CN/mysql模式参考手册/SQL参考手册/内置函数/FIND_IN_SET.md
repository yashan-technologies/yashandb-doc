```ebnf
find_in_set = FIND_IN_SET "(" str_expr ", " str_list_expr ")".
```

FIND_IN_SET函数返回目标字符串str_expr在字符串列表str_list_expr中的位置，字符串列表中的不同字符串用`,`隔开，返回的位置类型为INTEGER。

**str_list_expr**

表示一组字符串列表，不同字符串用`,`隔开，须为字符型或可转换为字符型的其他类型。

**str_expr**

表示需要在字符串列表中搜索匹配的字符串，须为字符型或可转换为字符型的其他类型，大小写敏感。

示例（HEAP表）

```sql
select find_in_set('yashan', 'mysql,YASHAN,yashan,oracle') res;

                    res 
----------------------- 
                      2

```
