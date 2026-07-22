
**condition::=**

```ebnf
= ["("] (([NOT] expr)|
expr ("<"|"<="|">"|">="|"="|"<>"|"!=") (((ANY|SOME|ALL) (subquery|expr_list))|expr|subquery)|
((expr [NOT] IN (expr_list|subquery))|(expr_list [NOT] IN (expr_list|subquery)))|
expr [NOT] LIKE expr [ESCAPE esc_char]|
expr [NOT] RLIKE expr|
([NOT] EXISTS) subquery|
expr IS ([NOT] NULL)|
expr IS ([NOT] EMPTY)|
expr [NOT] BETWEEN expr AND expr|
rownum_clause|
start_date1 end_date1 OVERLAPS start_date2 end_date2) [")"] 
[(AND|OR) condition].
```

**[expr及expr_list定义](./expr)**

condition为条件子句，被应用于WHERE、CHECK等需要进行条件判断的语法场景中，condition将返回TRUE或FALSE值。

YashanDB支持符合SQL标准的各类条件指定：

*   NOT

*   =|>|>=|<|<=|!=|<> [ANY|SOME|ALL]
*   IN|NOT IN
*   LIKE|NOT LIKE \[ESCAPE\]
*   RLIKE|NOT RLIKE
*   EXISTS|NOT EXISTS
*   IS NULL|IS NOT NULL

*   IS EMPTY|IS NOT EMPTY

*   BETWEEN...AND...
*   ROWNUM
*   能产生BOOLEAN值的任何表达式

## 比较条件

通过比较符（=|>|>=|<|<=|!=|<>）对其左右两边的表达式进行比较，获得布尔结果。

### ANY|SOME

ANY|SOME语法从比较符（=|>|>=|<|<=|!=|<>）右边的集合中选出符合比较条件的子集，然后对左右两边逐一比较，只要获得一个TRUE值，则返回TRUE；全部为FALSE值时，返回FALSE；右边集合为子查询且为空时，返回FALSE。

右边的集合可以为表达式列表，或者子查询语句；对于不指定ANY|SOME的比较，比较符的右边只能为产生单行结果的表达式。

当比较符的左右任一边出现NULL时，均返回FALSE。

示例

```sql
--ANY与SOME效果相同
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO < ANY('0303','0401');
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0001      深圳                                                         
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0201      Chengdu         
 
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE area_no <> SOME(SELECT area_no FROM area WHERE area_no IN ('01','02'));
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0401      Beijing                                                         
0402      Tianjin                                                         
0403      Dalian                                                         
0404      Shenyang                                                         
0201      Chengdu                                                         
0502      Changsha
```

### ALL

ALL语法从比较符（=|>|>=|<|<=|!=|<>）右边的集合中选出符合比较条件的子集，然后对左右两边逐一比较，只要获得一个FALSE值，则返回FALSE；全部为TRUE值时，返回TRUE；右边集合为子查询且为空时，返回TRUE。

右边的集合可以为表达式列表，或者子查询语句；对于不指定ALL的比较，比较符的右边只能为产生单行结果的表达式。

当比较符的左右任一边出现NULL时，均返回FALSE；但对于 NULL > ALL（结果为空的集合），返回TRUE。

示例

```sql
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE branch_no != ALL('0001','0303','0401','0403');
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0201      Chengdu
0402      Tianjin
0404      Shenyang                                                         
0502      Changsha
```

## IN条件

IN语法将左边集合里的值与右边集合里的值逐一做相等比较，左边的值全部命中则返回TRUE，否则返回FALSE。

右边的集合可以为表达式列表，或者子查询语句；当左边为表达式列表时，右边必须为表达式列表的集合，子查询则返回的是对应列表列项的结果集。

示例（HEAP表）

```sql
--行存表
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES b
WHERE (branch_no,area_no) IN
(SELECT b.branch_no,area_no FROM area a
WHERE a.area_no IN ('01','02')
AND a.area_no=b.area_no);
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen                                                         
0201      Chengdu
```



## LIKE条件

LIKE语法需要指定匹配的字符表达式char1与模式串char2，通过匹配算法将字符表达式char1与模式char2做匹配，匹配成功则返回TRUE，匹配失败则返回FALSE。其完整语法形式为：

char1 \[NOT\] LIKE char2 \[ESCAPE esc\_char\].

其中，char1表示要匹配的字符表达式，char2表示要匹配的模式，esc\_char表示escape字符。

LIKE语句执行如下规则：

*   char1，char2，和esc\_char都可以是一个表达式，其结果可以是任何数据类型，当它们的数据类型不一致时，都将转为VARCHAR类型进行判断。
*   当char1或者char2为NULL时，返回NULL。
*   char2可以包含特殊的模式匹配字符：下划线 （\_） 表示与值中的一个字符完全匹配；百分比符号（%）表示可以匹配值中的零或多个字符（不包括空）。

*   escape用于转义特殊的模式匹配字符，即将%或\_转变为本身的字面意思。如指定了escape，则char2中的esc\_char后字符必须为%或\_或esc\_char自身，否则返回YAS-04428或YAS-04429错误。

*   esc\_char后字符为esc\_char自身，表示将其转变为本身的字面意思，如当esc\_char为`/`时，模式`//`匹配的就是`/`这个字符，但`///`中的第三个`/`将作为转义符。

*   esc\_char必须是长度1的字符，也可以是运算后变成长度为1的字符。

示例

```sql
--完全匹配
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO||'11/1' LIKE '_1__11_1';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen       
 
--转义后无数据满足条件
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO||'11/1' LIKE '_1__11/_1' ESCAPE '/';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------

--改为本身字符查找，且去掉其后的_通配符后可以查找到数据
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO||'11/1' LIKE '_1__11//1' ESCAPE '/';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen
```

## RLIKE条件



RLIKE语法通过正则表达式匹配算法将字符表达式expr与正则表达式regexp做匹配，匹配成功返回TRUE，匹配失败返回FALSE。其完整语法形式为：



expr \[NOT\] RLIKE regexp.

RLIKE语句执行如下规则：

*   expr为要匹配的字符表达式，须为字符型或可转换为字符型的其他类型。
*   regexp为要匹配的[RegExp](./RegExp)，须为字符型或可转换为字符型的其他类型，长度不超过512字节。
*   当expr或者regexp为NULL时，返回NULL。

示例

```sql
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO RLIKE '01[0-9]+';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0101      Shanghai                                                         
0102      Nanjing                                                         
0103      Fuzhou                                                         
0104      Xiamen
```

## BETWEEN AND条件

BETWEEN  AND语法确定一个表达式的值是否在其他两个表达式定义的间隔内。在语句中出现的三个表达式都必须是数值型、字符型或日期时间型表达式，当三个表达式的数据类型不一致时，会先进行数据类型的转换，转换失败时返回错误。

示例

```sql
SELECT BRANCH_NO,BRANCH_NAME
FROM BRANCHES
WHERE BRANCH_NO NOT BETWEEN '01' AND '05';
BRANCH_NO BRANCH_NAME                                                     
--------- ----------------------------------------------------------------
0001      深圳                                                         
0501      Wuhan                                                         
0502      Changsha
```




## OVERLAPS

OVERLAPS是专门用于比较时间日期区间是否有重叠的函数，当时间区间有交集时返回true，反之返回false。语法格式如下：

(start_date1, end_date1) OVERLAPS (start_date2, end_date2) 

OVERLAPS函数执行遵循以下规则：

*   表达式start_date1、end_date1、start_date2、end_date2不可缺省，可以输入null或空串。
*   四个表达式的类型需要一致，并且参数类型为DATE、TIMESTAMP、TIME，其他类型则报错。
*   返回类型为BOOL类型，输出结果为true或false。
*   区间定义范围为开区间。
*   当一侧区间定义全为null时会报错。

示例

```sql
-- 判断时间区间(2022-01-01, 2024-03-05)与区间(null, 2024-01-05)是否存在交集
select 1 from dual where (to_date('2022-01-01', 'YYYY-MM-DD'), to_date('2024-03-05', 'YYYY-MM-DD')) overlaps (null, to_date('2024-01-05', 'YYYY-MM-DD'));

           1 
------------ 
           1

1 row fetched.
```
