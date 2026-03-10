```ebnf+diagram
group_concat::= GROUP_CONCAT "(" [DISTINCT] (string) {"," (string)} [order_by_clause [SEPARATOR sep_character]] ")"
```

GROUP_CONCAT函数在[CONCAT](./CONCAT)函数的功能上增加了聚集功能，即对GROUP BY聚集的每个分组里的多行执行CONCAT操作，函数返回值是CLOB类型。

本函数不支持向量化计算。

**DISTINCT**

过滤在同一组内出现的相同string。  


目前，YashanDB无法对LOB类型的数据进行去重。


**string**

string须为字符型或可转换为字符型的其他类型，但不允许为JSON、时区类型。

当string为NULL时，函数返回NULL。

string参数可以为：

- [expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)
- 查询列为单列且返回行为单行的子查询

string参数数量上限为4096。

**order_by_clause**

对组内要CONCAT的string排序，其语法与SELECT语句中描述一致。

当ORDER BY后指定的是常量数字时，表示的是string的顺序值。

**SEPARATOR sep_character**

指定将组内的多行进行CONCAT时，多行之间加上sep_character定义的分隔符，sep_character只能为character类的常量或NULL。

此语句不指定时，默认为','。

示例（HEAP表）

```sql
--创建exprs_group表，并插入数据
CREATE TABLE exprs_group (expra INT,exprb CHAR(3));
INSERT INTO exprs_group VALUES(1, 'aaa');
INSERT INTO exprs_group VALUES(1, 'bbb');
INSERT INTO exprs_group VALUES(2, 'bbb');
INSERT INTO exprs_group VALUES(2, 'ccc');
INSERT INTO exprs_group VALUES(3, 'ccc');
INSERT INTO exprs_group VALUES(3, 'ddd');
COMMIT;
 
--未指定GROUP BY时，将所有行CONCAT，得到一行结果
SELECT GROUP_CONCAT(expra,exprb) res FROM exprs_group;
RES                                                             
----------------------------------------------------------------
1aaa,1bbb,2bbb,2ccc,3ccc,3ddd                                  
 
--group by后各组的多行数据分别CONCAT成一行，得到按组的多行结果
SELECT expra, GROUP_CONCAT(expra,exprb ORDER BY 2 SEPARATOR '$') res
FROM exprs_group
GROUP BY expra;
       EXPRA RES                                                             
------------ ----------------------------------------------------------------
           1 1aaa$1bbb                                                      
           2 2bbb$2ccc                                                      
           3 3ccc$3ddd      
 
--子查询
SELECT expra,GROUP_CONCAT((SELECT expra FROM exprs_group WHERE ROWNUM=1),exprb ORDER BY 2 SEPARATOR '$') res
FROM exprs_group
GROUP BY expra;
       EXPRA RES                                                             
------------ ----------------------------------------------------------------
           1 1aaa$1bbb                                                      
           2 1bbb$1ccc                                                      
           3 1ccc$1ddd
```
