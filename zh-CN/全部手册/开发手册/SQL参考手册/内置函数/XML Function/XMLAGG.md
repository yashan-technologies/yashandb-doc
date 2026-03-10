```ebnf+diagram
xmlagg::= XMLAGG "(" xml_data [order_by_clause]")"
```

XMLAGG是一个聚合函数，用于将多行XML数据合并为一个XMLTYPE类型的值，返回类型是XMLTYPE。

**xml_data**

[通用表达式](../../通用SQL语法/expr)，待聚合的XML数据，遵循如下规则：

* 只能是XMLTYPE类型。
* 数据长度不得超过64MB。
* 若输入为NULL，则返回值也为NULL。

**order_by_clause**

对组内要聚合的xml_data排序，其语法与SELECT语句中描述一致。

当ORDER BY后指定的是常量数字时，表示的是xml_data的顺序值。

示例（HEAP表）

```sql
--将employee_data中的xml_data数据聚合起来
SELECT XMLAGG(xml_data ORDER BY id).GetClobVal() res FROM employee_data;

RES
----------------------------------------------------------------
<employee id="101">
                <name>John Smith</name>
                <salary>8500</salary>
              </employee><employee id="102">
                <name>Jane Doe</name>
                <salary>9200</salary>
              </employee><employee id="103">
                <name>Robert Johnson</name>
                <salary>7800</salary>
              </employee><employee id="104">
                <name>Emily Brown</name>
                <salary>9500</salary>
              </employee><employee id="105">
                <name>Michael Davis</name>
                <salary>8200</salary>
              </employee>


--输入为NULL，则返回空
SELECT XMLAgg(NULL).GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
```
