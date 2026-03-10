```ebnf+diagram
NEXT_DAY::= NEXT_DAY "(" expr1 "," expr2 ")" 
```

NEXT_DAY函数根据expr2的值，计算expr1表示的日期的下一个星期几，返回一个DATE类型数值。

**expr1**

YashanDB认可的[通用表达式](../通用SQL语法/expr)，其值须为DATE/TIMESTAMP/时区类型或可转换为TIMESTAMP类型的字符型。

当expr1的值为NULL时，函数返回NULL。

函数从expr1表示日期的第二天开始计数，获得下一个星期几的时间，且计算结果的时分秒与expr1的时分秒部分一致。

**expr2**

YashanDB认可的[通用表达式](../通用SQL语法/expr)，其值须为数值型或字符型，对于非整数的数值型数据，函数对其进行向下取整。

当expr2的值为NULL时，函数返回NULL。

当为数值型时，expr2的值范围为`[1,8)`，超出范围报错。

当为字符型时，expr2的值只能为如下列示字符串中的某一个（不区分大小写，且函数将忽略字符串右侧空格）：

- 星期缩写mon、tue、wed、thu、fri、sat、sun。
- 星期全称Monday、Tuesday、Wednesday、Thursday、Friday、Saturday、Sunday。



示例

```sql
--对空值返回空
SELECT NEXT_DAY(null,null) res FROM DUAL;
RES              
--------------------------------                           

--从2022-09-23 07:00:00的第二天开始计算下一个星期五
SELECT TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss') "cur-year-month-day",
TO_CHAR(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),'yyyy-mm-w-day') "cur-year-month-week-day",
NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),6) "year-month-day",
TO_CHAR(NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),6),'yyyy-mm-w-day') "year-month-week-day"
FROM DUAL;
cur-year-month-day      cur-year-month-week-day    year-month-day         year-month-week-day   
----------------------- -------------------------- ---------------------- --------------------- 
2022-09-23 07:00:00     2022-09-4-friday           2022-09-30 07:00:00    2022-09-5-friday     

--以'fri'作为参数
SELECT NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),'fri') "year-month-day",
TO_CHAR(NEXT_DAY(TO_DATE('2022-09-23 07:00:00','yyyy-mm-dd hh24:mi:ss'),'fri'),'yyyy-mm-w-day') "year-month-week-day"
FROM DUAL;
year-month-day                   year-month-week-day    
-------------------------------- ----------------------------------
2022-09-30 07:00:00              2022-09-5-friday     

--返回DATA类型结果
SELECT NEXT_DAY(TIMESTAMP '2022-09-23 10:00:00.99','friday') "year-month-day",
TO_CHAR(NEXT_DAY(TIMESTAMP '2022-09-23 10:00:00.99','friday'),'yyyy-mm-w-day') "year-month-week-day"
FROM DUAL;
year-month-day                   year-month-week-day       
-------------------------------- ----------------------------
2022-09-30 10:00:00              2022-09-5-friday   
```

