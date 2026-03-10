```ebnf+diagram
substring_index::= SUBSTRING_INDEX "(" expr "," delim "," count ")"
```

SUBSTRING_INDEX函数用于在源字符串[expr](../通用SQL语法/expr)中提取分隔符delim出现第count次前的子字符串。

**expr**

通用表达式，其值须为字符型或除JSON、LOB、XMLTYPE类型外的可转化为字符型的其他类型。

- 当expr为NCHAR/NVARCHAR类型时返回值为NVARCHAR类型，其余场景返回值为VARCHAR类型。

- 当expr的值为NULL时，函数返回NULL。

**delim**

分隔符，用于分割源字符串expr。delim为与expr相同的通用表达式，须为字符型数据或除JSON、LOB、XMLTYPE类型外的可转化为字符型的其他类型数据。

- 若使用非字符型的形式输入`(0,1)`之间的小数，转换后的delim将去除小数点前的0进行匹配。

- 当delim在expr中未匹配到时，函数将返回expr全部内容。

- 当delim的值为NULL时，函数返回NULL。

**count**

表示分隔符出现的次数，用于定位分割的终止位置。count为与expr相同的通用表达式，须为除BIT外数值型数据或可转换为NUMBER的其他类型数据，取值范围为[-2147483648,2147483647]。值为正数表示返回分隔符第count次出现时左侧的所有内容（从左侧开始计数），值为负数表示返回分隔符第count次出现时右侧的所有内容（从右侧开始计数）。

- 当count的值为带有小数的NUMBER类型时，函数将其四舍五入取整。

- 当count的值为浮点类型时，函数将其奇进偶舍取整。

- 当count的值超出delim在expr中的出现次数时，函数将返回expr全部内容。

- 当count的值为NULL或0时，函数返回NULL。

示例

```sql
SELECT SUBSTRING_INDEX('192.168.0.1',0.1, 1) a
,SUBSTRING_INDEX('192.168.0.1','.', 2) b
,SUBSTRING_INDEX('192.168.0.1','.', 3) c
,SUBSTRING_INDEX('192.168.0.1','.', 4) d
FROM DUAL;
A     B         C             D             
----- --------- ------------- ------------- 
192   192.168   192.168.0     192.168.0.1  

SELECT SUBSTRING_INDEX('192.168.0.1','.',-1) a
,SUBSTRING_INDEX('192.168.0.1','.',-2) b
,SUBSTRING_INDEX('192.168.0.1','.',-3) c
,SUBSTRING_INDEX('192.168.0.1','.',-4) d
FROM DUAL;
A     B     C         D             
----- ----- --------- ------------- 
1     0.1   168.0.1   192.168.0.1  

SELECT SUBSTRING_INDEX('192.168.0.1','.',CAST(1.5 AS NUMBER)) a
,SUBSTRING_INDEX('192.168.0.1','.',CAST(2.5 AS NUMBER)) b
,SUBSTRING_INDEX('192.168.0.1','.',CAST(1.5 AS FLOAT)) c
,SUBSTRING_INDEX('192.168.0.1','.',CAST(2.5 AS FLOAT)) d
,SUBSTRING_INDEX('192.168.0.1','.',CAST(1.5 AS DOUBLE)) e
,SUBSTRING_INDEX('192.168.0.1','.',CAST(2.5 AS DOUBLE)) f
FROM DUAL;
A         B             C         D         E         F         
--------- ------------- --------- --------- --------- --------- 
192.168   192.168.0     192.168   192.168   192.168   192.168  

SELECT SUBSTRING_INDEX(NULL,'.',1) a
,SUBSTRING_INDEX('192.168.0.1',NULL,1) b
,SUBSTRING_INDEX('192.168.0.1','.',NULL) c
,SUBSTRING_INDEX('192.168.0.1','.',0) d
FROM DUAL;
A     B     C     D     
----- ----- ----- ----- 

```
