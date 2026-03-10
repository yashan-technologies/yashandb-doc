```ebnf+diagram
listagg::= LISTAGG "(" [ALL|DISTINCT] expr ["," separator] [listagg_overflow_clause] ")"
  [WITHIN GROUP order_by_clause] [OVER query_partition_clause]
```

LISTAGG函数将多行的[expr](../通用SQL语法/expr)执行拼接操作，并通过分隔符分隔，返回一行VARCHAR/RAW类型的字符串。

**expr**

expr不能为BIT、BOOLEAN、时区类型和UDT类型。

当expr的类型为RAW时，函数返回RAW类型，否则函数返回VARCHAR类型。

**DISTINCT**

计算最终拼接结果时，过滤在同一组内出现的重复的行。


目前，YashanDB无法对LOB类型的数据进行去重。


**ALL**

默认值，表示不过滤重复的行，对所有行都进行拼接。

**separator**

用于定义分隔符。不指定separator时，默认分隔符为NULL。

separator可以为常量（含常量表达式）或绑定参数的变量表达式，其数据类型不能为BOOLEAN、BIT、CLOB、BLOB、JSON、UDT和NCLOB。

当expr为RAW类型时，separator必须为RAW（或可以隐式转换为RAW）类型。

当expr为NULL时，该行数据会被忽略。

**listagg_overflow_clause**

```ebnf+diagram
listagg_overflow_clause::= (on overflow error | on overflow truncate ["'" text "'"] [(with | without) count])
```

当终止符或分隔符长度超过8000或者（分隔符长度）+ （终止符长度）+ （withcount26个字节）超过8000， 直接报错。

当拼接的结果发生溢出时候（即拼接的expr+分隔符>8000时）进行特殊处理：

- on overflow error：默认选项，发生溢出时直接报错。
- on overflow truncate(text)：text是指定的终止标志（即终止符），在发生溢出后需要预留位置用来放置终止标志，默认终止标志为'...'占3个字节。
  - with count：发生溢出时且使用truncate时，默认输出count。在发生溢出后需要预留26个字节（给count预留24个字符，括号预留2个字符）。将已经拼接的行数回滚到可以放置需要给"(count)"和终止标志预留的字节数，将最终溢出的行数赋值给count。使用方法例如on overflow truncate '*' with count。
  - without count：在截断后面不显示截断的行数，发生溢出时需要为终止标志预留位置，不需要为"(count)"预留位置。例如on overflow truncate '*' without count。

指定终止符退化成默认的终止符的情况：

- 如果是without count，当终止符或分隔符长度超过4000或终止符+分隔符长度超过4000，终止符退化成'...'显示，并且不显示分隔符；如果前面条件不成立，那么会显示可以显示的行数+分隔符+终止符（可显示的行数可能为0）。
- 如果是with count或缺省，当终止符或分隔符长度超过4000-2-24 = 3974或终止符+分隔符长度超过3974，显示count，终止符退化成'...'显示，并且不显示分隔符；如果前面条件不成立，那么会显示可以显示的行数+分隔符+终止符+count（可显示的行数可能为0）。

**within group order_by_clause**

对组内需要拼接的数据进行排序后再拼接。

作为非窗口函数时，本关键字仅适用于HEAP表。

示例（HEAP表）

```sql
--创建exprs表，并插入数据
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , name VARCHAR(8000) , class RAW(300));
INSERT INTO exprs VALUES(1,'carrot','012');
INSERT INTO exprs VALUES(3,'rabbit','0ea');
INSERT INTO exprs VALUES(3,'carrot','66734');
INSERT INTO exprs VALUES(2,'clion','012');
INSERT INTO exprs VALUES(3,'apple','12900');
COMMIT;

--普通的数据拼接
SELECT LISTAGG(name) res FROM exprs;
RES
----------------------------------------------------------------
carrotrabbitcarrotclionapple                                    

SELECT LISTAGG(class,'00a') res FROM exprs;
RES
----------------------------------------------------------------
0012000A00EA000A066734000A0012000A012900          

--使用DISTINCT关键字去重
SELECT LISTAGG(DISTINCT name ,' ;') res FROM exprs;
RES
----------------------------------------------------------------
apple ;carrot ;clion ;rabbit
    
--使用within group(order by)进行行内排序
SELECT LISTAGG(name,';') within GROUP(ORDER BY name) FROM exprs;
NAME
----------------------------------------------------------------
apple;carrot;carrot;clion;rabbit

--使用group by进行分组
SELECT id,LISTAGG(name,' ') within GROUP(ORDER BY name) FROM exprs GROUP BY id;
          ID NAME
------------ ----------------------------------------------------------------
           1 carrot
           3 apple carrot rabbit
           2 clion

--使用within group + partition对窗口内数据排序
SELECT id,LISTAGG(name,' ') within GROUP(ORDER BY name) OVER (PARTITION BY name) name FROM exprs ORDER BY 1,2;
          ID NAME
------------ ----------------------------------------------------------------
           1 carrot carrot
           2 clion
           3 apple
           3 carrot carrot
           3 rabbit

-- 多插入几行数据使能发生溢出情况
DELETE exprs;
INSERT INTO exprs(name) VALUES(LPAD('as',4000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('dj',3000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('sd',500,'cdua'));
INSERT INTO exprs(name) VALUES(LPAD('sss',200,'cpd'));
INSERT INTO exprs(name) VALUES(LPAD('asiufs',100,'cod'));
INSERT INTO exprs(name) VALUES(LPAD('affs',300,'cdd'));

--发生溢出时候的操作1：直接报错
SELECT LISTAGG(name,';' ON overflow error) FROM exprs;
YAS-02511 result of string concatenation value exceeds maximum length of 8000 characters

--发生溢出时候的操作2：截断
SELECT LISTAGG(name,';' ON overflow TRUNCATE) res FROM exprs;
--省略部分输出
......ss;codcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcasiufs;...(1)
```

示例（TAC表、LSC表）

```sql
-- 创建exprs表，并插入数据
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , name VARCHAR(8000) , class RAW(300));
INSERT INTO exprs VALUES(1,'carrot','012');
INSERT INTO exprs VALUES(3,'rabbit','0ea');
INSERT INTO exprs VALUES(3,'carrot','66734');
INSERT INTO exprs VALUES(2,'clion','012');
INSERT INTO exprs VALUES(3,'apple','12900');
COMMIT;

-- 普通的数据拼接
SELECT LISTAGG(name) res FROM exprs;
RES
----------------------------------------------------------------
carrotrabbitcarrotclionapple                                    

SELECT LISTAGG(class,'00a') res FROM exprs;
RES
----------------------------------------------------------------
0012000A00EA000A066734000A0012000A012900          

-- 使用DISTINCT关键字去重
SELECT LISTAGG(DISTINCT name ,' ;') res FROM exprs;
RES
----------------------------------------------------------------
apple ;carrot ;clion ;rabbit

--使用within group + partition对窗口内数据排序
SELECT id,LISTAGG(name,' ') within GROUP(ORDER BY name) OVER (PARTITION BY name) name FROM exprs ORDER BY 1,2;
          ID NAME
------------ ----------------------------------------------------------------
           1 carrot carrot
           2 clion
           3 apple
           3 carrot carrot
           3 rabbit

-- 多插入几行数据使能发生溢出情况
DELETE exprs;
INSERT INTO exprs(name) VALUES(LPAD('as',4000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('dj',3000,'cad'));
INSERT INTO exprs(name) VALUES(LPAD('sd',500,'cdua'));
INSERT INTO exprs(name) VALUES(LPAD('sss',200,'cpd'));
INSERT INTO exprs(name) VALUES(LPAD('asiufs',100,'cod'));
INSERT INTO exprs(name) VALUES(LPAD('affs',300,'cdd'));

-- 发生溢出时候的操作1：直接报错
SELECT LISTAGG(name,';' ON overflow error) FROM exprs;
YAS-05005 result of string concatenation value exceeds maximum length of 8000 characters

-- 发生溢出时候的操作2：截断
SELECT LISTAGG(name,';' ON overflow TRUNCATE) res FROM exprs;
-- 省略部分输出
......ss;codcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcodcasiufs;...(1)
```

**OVER**

当指定OVER关键字时，LISTAGG将作为[窗口函数](00内置函数.html#WindowFunction)，将每个分组窗口内的行进行拼接，分组窗口内每一行的拼接结果都相同。

**query_partition_clause**

窗口函数通用语法。

示例

```sql
-- 创建exprs表，并插入数据
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , name VARCHAR(8000) , class RAW(300));
INSERT INTO exprs VALUES(1,'carrot','012');
INSERT INTO exprs VALUES(3,'rabbit','0ea');
INSERT INTO exprs VALUES(3,'carrot','66734');
INSERT INTO exprs VALUES(2,'clion','012');
INSERT INTO exprs VALUES(3,'apple','12900');
COMMIT;

-- 查询并拼接
SELECT id,name,
LISTAGG(name, ',') OVER (PARTITION BY id) c1,
MAX(name) OVER (PARTITION BY id ORDER BY name) c2
FROM exprs;
 ID NAME       C1                     C2        
--- ---------- ---------------------- --------- 
  1 carrot     carrot                 carrot   
  2 clion      clion                  clion    
  3 apple      rabbit,carrot,apple    apple    
  3 carrot     rabbit,carrot,apple    carrot   
  3 rabbit     rabbit,carrot,apple    rabbit  
```
