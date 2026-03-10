DBMS_DATAMASK提供了数据脱敏接口，用于对单列数据进行脱敏操作。

执行该高级包的用户只能为sys用户或具备SECURITY_ADMIN角色的用户。

分布式部署中无数据脱敏相关功能。

## ADD\_RULE

```plsql
DBMS_DATAMASK.ADD_RULE(
    POL_NAME   IN  VARCHAR,
    OBJ_NAME   IN  VARCHAR,
    USERNAME   IN  VARCHAR,
    FUNDESC    IN  VARCHAR,
    PARAMLIST  IN  VARCHAR);
```
DBMS_DATAMASK.ADD_RULE用于为单列数据添加脱敏策略。

本高级包遵循如下规则：

- 仅支持在HEAP表的非隐藏列上增加脱敏策略。

- 无法对具备脱敏策略的列进行filter运算，否则返回错。

- 如果对非空列添加使数据返回空的策略，在查询阶段返回错误。

| 参数                   | 描述                                                                                                                                            |
|:---------------------|:----------------------------------------------------------------------------------------------------------------------------------------------|
| POL_NAME             | 策略名，只能为CHAR或VARCHAR类型，最长64字节，不允许为空，大小写敏感，唯一标识一条策略。                                                                                            |
| OBJ_NAME             | 添加策略的对象，只能为CHAR或VARCHAR类型，各字段最长64字节，不允许为空，格式为schema.table.column或table.column，后者应用schema为当前用户，指对schema用户下的table表中的column列添加策略。<br/>单次只能指定1个列。                 |
| USERNAME             | 策略应用到的用户，只能为CHAR或VARCHAR类型，最长64字节。<br/>仅支持指定为单个用户名、ALL或空串，为空时采用默认值ALL，表示应用到所有用户。              |
| FUNDESC             | 脱敏策略，只能为CHAR或VARCHAR类型，最长64字节。<br/>取值可选项包括EMAIL_MASK、PARTIAL、DEFAULT或空串，为空时采用默认值DEFAULT，具体的脱敏策略详情如下文所述。                |
| PARAMLIST            | 选择PARTIAL策略时应用的参数，只能为CHAR或VARCHAR类型，最长4000字节，格式为'n,m'，表示保留前n位和后m位。<br />仅当FUNDESC指定为PARTIAL时可配置该参数，指定为其它策略时该参数只能为空或`','`（等价于空）。 |

目前支持的脱敏策略：

- DEFAULT：默认脱敏策略，各类型数据脱敏结果如下：
- DEFAULT: Default masking strategy. The masking results for various data types are as follows:

  - 数字类型：0（不保证精度）

  - 布尔类型：FALSE
  - Boolean type: FALSE

  - 时间类型（除TIME类型外）：年月日部分1970-01-01，时分秒部分00:00:00

  - 其他类型：空值

- PARTIAL：部分脱敏（又称“自定义脱敏”），支持对VARCHAR/CHAR类型数据进行脱敏，其他类型返回空值。

  - 当使用PARTIAL策略且PARAMLIST参数不为空时，有且只能有两个正数参数，若参数为小数则四舍五入为正整数，两个参数之间使用逗号','隔开。其中第一个参数为保留的前n位，第二个参数为保留的后m位，其余位用*进行脱敏。例如参数传入`1,1`，"test"脱敏为"t**t"。

  - 当使用PARTIAL策略且PARAMLIST参数为空串或指定的保留位数（无论前后）超过字符串长度时，不对数据进行脱敏操作，输出原字符串。
  
- EMAIL_MASK：邮件格式字符串专用脱敏，支持对VARCHAR/CHAR类型数据进行脱敏，其他类型返回空值。数据脱敏结果中email地址将仅保留@符号，其他字符全用`*`替换，例如"123@sics.ac.cn"脱敏为"*\*\*\*@**********"。

示例（HEAP表）

```plsql
-- 未创建脱敏策略时，数据直接呈现实际值
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      2022-06-17 10:45:14
0101000002                John                      2019-09-21 10:45:14
0201010011                Anna                      2024-05-17 10:45:14
0201008003                Jack                      2023-04-13 10:45:14
0201008004                Jim                       2024-08-25 10:45:14

-- 为employees表的employee_name列创建脱敏策略pol1，规则为部分脱敏（前后各保留1位）
SELECT dbms_datamask.add_rule('pol1','employees.employee_name','sales','partial','1,1') pol1 FROM dual;

        POL1
------------
            
-- 为employees表的entry_date列创建脱敏策略pol2，采用默认规则（日期类型替换为1970-01-01 00:00:00）
SELECT dbms_datamask.add_rule('pol2','employees.entry_date','sales','','') pol2 FROM dual;

        POL2
------------

-- 数据查询结果集呈现为脱敏后的内容
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00
```

## DEL\_RULE

```plsql
DBMS_DATAMASK.DEL_RULE(
    POL_NAME   IN  VARCHAR);
```
DBMS_DATAMASK.DEL_RULE用于为单列数据删除脱敏策略。

示例（HEAP表）
```plsql
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00

-- 删除脱敏策略pol1
SELECT dbms_datamask.del_rule('pol1') FROM dual;

DBMS_DATAMASK.DEL_RU
--------------------

-- 数据查询结果集中，employee_name列的数据呈现为原始数据
SELECT employee_no,employee_name,entry_date FROM employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      1970-01-01 00:00:00
0101000002                John                      1970-01-01 00:00:00
0201010011                Anna                      1970-01-01 00:00:00
0201008003                Jack                      1970-01-01 00:00:00
0201008004                Jim                       1970-01-01 00:00:00
```
