```ebnf+diagram
nlssort::= NLSSORT "(" expr ["," "'" NLS_SORT "=" collation "'"] ")"
```

NLSSORT函数根据指定的排序规则对[expr](../通用SQL语法/expr)的值进行排序，生成一个VARCHAR类型的排序键。

本函数只能在UTF-8、GB18030字符集下运行。

本函数一般应用于ORDER BY语句中，此时函数生成的是包含每一行排序键的整体排序键，且在某一行的排序键为NULL时，遵循ASC时NULLS LAST以及DESC时NULLS FIRST原则。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型（LOB、BFILE类型支持隐式转换）。

- expr不能为超过65534字节的BFILE、LOB类型数据。
- 当expr的值为NULL时，函数返回NULL。

**NLS_SORT**

指定排序规则，可省略，默认为CHINESE_PINYIN。YashanDB支持如下排序规则：

- CHINESE_PINYIN：区分大小写的拼音排序，与SCHINESE_PINYIN_M等价
- CHINESE_PINYIN_CI：不区分大小写的拼音排序，与SCHINESE_PINYIN_M_CI等价

在CHINESE_PINYIN排序规则下的排序键构造规则为：

- 对每个字符（空格除外），按其Unicode值（十六进制）生成一个四位的字符串的排序规则键，例如对'a'生成'0061'。
- 对不含空格的字符串，其排序键由其所有字符的排序规则键拼接，并在最后加上'000001..01'组成，其中'01'的个数对应字符的个数，例如对'aA'生成'0061004100000101'。
- 对只包含空格的字符串，无论空格数量多少均生成'00000020'的排序规则键。
- 对部分包含空格的字符串，忽略出现在字符串末尾的空格，其他空格则在'000001..01'中按照空格出现的位置相应增加'20'，例如对'a(空格符1)(空格符2)(空格符3)A(空格符4)(空格符5) '生成'0061004100000120202001'。

在CHINESE_PINYIN_CI排序规则下的排序键构造规则为：

- 对每个字符（空格除外），先将小写转换为大写，再按其Unicode值（十六进制）生成一个四位的字符串的排序规则键，例如对'a'生成'0041'。
- 对不含空格的字符串，其排序键由其所有字符的排序规则键拼接，例如对'aA'生成'00410041'。
- 对只包含空格的字符串，无论空格数量多少均生成'0000'的排序规则键。
- 对部分包含空格的字符串，忽略所有空格，例如对'a(空格符1)(空格符2)(空格符3)A(空格符4)(空格符5) '生成'00410041'。

示例

```sql
-- 更新employees表中John员工名称为join
UPDATE employees SET employee_name='join' WHERE employee_name='John';
 
-- 指定不同排序规则对其按姓名排序
SELECT employee_name n,NLSSORT(employee_name) s1,
NLSSORT(employee_name,'NLS_SORT=CHINESE_PINYIN_CI') s2
FROM employees;
N         S1                                 S2                 
--------- ---------------------------------- --------------------
Mask      004D00610073006B000001010101       004D00410053004B   
join      006A006F0069006E000001010101       004A004F0049004E   
Anna      0041006E006E0061000001010101       0041004E004E0041   
Jack      004A00610063006B000001010101       004A00410043004B   
Jim       004A0069006D0000010101             004A0049004D                       
 
SELECT employee_name n
FROM employees
ORDER BY NLSSORT(employee_name);
N            
-------------
Anna        
Jack        
Jim         
Mask        
join 
 
SELECT employee_name n
FROM employees
ORDER BY NLSSORT(employee_name,'NLS_SORT=CHINESE_PINYIN_CI');
N            
-------------
Anna        
Jack        
Jim         
join        
Mask
```
