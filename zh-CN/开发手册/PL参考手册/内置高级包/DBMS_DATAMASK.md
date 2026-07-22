DBMS_DATAMASK提供了数据脱敏接口，用于对数据进行动态脱敏。



在YashanDB中使用数据脱敏，需遵守如下约束：

- 该高级包仅允许在无表查询的投影列中调用，且要求执行用户具备SECURITY_ADMIN角色或使用sys用户，具体语法格式为`SELECT DBMS_DATAMASK.ADD_RULE(……) …… FROM DUAL`，不能通过CALL语句、匿名块等PL语法进行调用。

- 数据脱敏仅适用于HEAP表的非UDT列与非隐藏列，且不能为临时表。

    >**Note**:
    >
    > 若在为某HEAP表的某个非UDT列正确配置脱敏策略后删除该表，再重建同名表和列但将该同名列的数据类型改为UDT，原脱敏策略将自动失效。

- 无法对配置了脱敏策略的列进行筛选、过滤运算。

- 不建议对非空约束列配置脱敏结果可能出现空值的策略，否则查询时会因空值而返回错误。

- 存算一体分布式集群部署中无数据脱敏相关功能。



## ADD_RULE

```plsql
DBMS_DATAMASK.ADD_RULE(
    POL_NAME                IN VARCHAR,
    OBJ_NAME                IN VARCHAR,
    USERNAME                IN VARCHAR,
    FUNDESC                 IN VARCHAR,
    PARAMLIST               IN VARCHAR,
    REGEXP_PATTERN          IN VARCHAR,
    REGEXP_REPLACE_STRING   IN VARCHAR,
    REGEXP_POSITION         IN BIGINT,
    REGEXP_OCCURRENCE       IN BIGINT,
    REGEXP_MATCH_PARAMETER  IN VARCHAR);
```
DBMS_DATAMASK.ADD_RULE用于为单列数据添加脱敏策略。

|  参数| 描述|
|:---------------------|:----------------------------------------------------------------------------------------------------------------------------------------------|
| POL_NAME             | 策略的名称，全局唯一，只能为CHAR或VARCHAR类型，最长64字节，不允许为空，大小写敏感。  |
| OBJ_NAME             | 添加策略的列字段，只能为CHAR或VARCHAR类型，最长64字节，不能为空，格式为'[schema.]table.column'，表示对schema用户下的table表中的column列添加策略，目标列所在表属于当前用户时可省略`schema.`。<br/>配置要求：<br/>* 必须指定为已存在的列<br/>* 目标列的数据类型不能为UDT<br/>* 单次只能指定1个列            |
| USERNAME             | 策略应用到的用户，只能为CHAR或VARCHAR类型，最长64字节。<br/>仅支持指定为单个已存在的用户名或空（NULL、空字符串或省略该参数），置空表示应用到所有用户。 |
| FUNDESC             | 脱敏函数的名称，只能为CHAR或VARCHAR类型，最长64字节。<br/>取值可选项包括EMAIL_MASK、PREFIX_EMAIL_MEDIUM、PREFIX_EMAIL_LOW、PARTIAL、RANDOM、REG_EXP、SHUFFEL、DEFAULT或空，置空时采用默认值DEFAULT，具体的脱敏策略详情如下文所述。 |
| PARAMLIST            | 自定义部分脱敏时需保持原状的字符位数，只能为CHAR或VARCHAR类型，最长4000字节，格式为`'n,m'`，表示前n位和后m位保持原状。n和m的值域应为[0，2<sup>63</sup>），指定为小数时按四舍五入取整。若n大于原数据总长度，则直接保留原数据不做脱敏处理。<br />仅当FUNDESC指定为PARTIAL时可配置该参数，指定为其它策略时该参数只能为空或`','`（等价于空）。 |
| REGEXP_PATTERN | 正则表达式匹配模式，即指定需要脱敏的字符串，只能为CHAR或VARCHAR类型，最长512字节。指定置空时，表示不进行脱敏直接返回真实数据。<br />仅当FUNDESC指定为REG_EXP时可配置该参数，指定为其它策略时该参数只能为空。 |
| REGEXP_REPLACE_STRING | 用于替换原数据的字符串，只能为CHAR或VARCHAR类型，最长4000字节。省略时，使用空字符串。<br />仅当FUNDESC指定为REG_EXP时可配置该参数，指定为其它策略时该参数只能为空。 |
| REGEXP_POSITION | 开始进行匹配的偏移量，需为BIGINT类型或可转换为BIGINT类型的其他类型，数值按四舍五入取整且取整后值域应为[1,65535]。可省略，默认值为1。<br />仅当FUNDESC指定为REG_EXP时可配置该参数，指定为其它策略时该参数只能为空。 |
| REGEXP_OCCURRENCE | 进行替换的目标位，需为BIGINT类型或可转换为BIGINT类型的其他类型，数值按四舍五入取整且取整后值域应为[0,65534]。可省略，默认值为0。<br />* 0：替换所有匹配的字符<br />* n：仅替换第n个匹配的字符<br />仅当FUNDESC指定为REG_EXP时可配置该参数，指定为其它策略时该参数只能为空。 |
| REGEXP_MATCH_PARAMETER | 正则表达式匹配参数，即指定正则匹配的大小写、换行、多行等规则，只能为CHAR或VARCHAR类型，最长10字节，取值为`c/i/n/m/x`的组合或空字符串<br />* c：区分大小写，默认区分<br />* i：不区分大小写<br />* n：允许句点（`.`）匹配换行符，默认不匹配<br />* m：多行模式，使用`^`匹配每行开头、`$`匹配每行结尾，默认只匹配整个字符串的首尾<br />* x：忽略空格字符，默认不忽略<br />* 空字符串：使用默认行为，即区分大小写、句点（`.`）不匹配换行、单行模式<br />仅当FUNDESC指定为REG_EXP时可配置该参数，指定为其它策略时该参数只能为空。 |



YashanDB支持的脱敏规则如下表所示，各类脱敏规则对于不适用的数据类型均会将其脱敏为空值。

| 脱敏函数| 规则类型| 适用的数据类型 | 脱敏效果|
| ------------ | -------------- | ---------------------- | --------------- |
| DEFAULT      | 默认完整脱敏   | CHAR、VARCHAR<br />TINYINT、SMALLINT、INT、BIGINT、FLOAT、DOUBLE、NUMBER、BIT<br />BOOLEAN<br />DATE、TIMESTAMP、LTZ、TZ、INTERVAL YEAR TO MONTH、INTERVAL DAY TO SECOND | * 字符型：空值<br />* 数值型：0（不保证精度）<br />* 布尔型：FALSE<br />* 日期时间型（TIME类型除外）：年月日部分1970-01-01，时分秒部分00:00:00 |
| EMAIL_MASK   | 邮箱脱敏       | CHAR、VARCHAR           | 按字符集进行数据脱敏：<br />* 邮箱字符串：保留邮箱地址中的`@`符号，其他部分逐位使用星号（`*`）替换，例如`yashan@sics.ac.cn`将脱敏为`******@**********`<br />* 非邮箱字符串：全部内容逐位使用星号（`*`）替换，例如`yashan`将脱敏为`******` |
| PREFIX_EMAIL_LOW | 邮箱前缀脱敏（低） | CHAR、VARCHAR           | 按字符集进行数据脱敏：<br />* 邮箱字符串：保留邮箱前缀的前3个字符并将前缀的其他部分使用3个星号（`***`）替换，若前缀少于等于3个字符则保留完整前缀并在前缀与`@`符号间追加3个星号（`***`），例如`yashan@sics.ac.cn`将脱敏为`yas***@sics.ac.cn`、`ya@sics.ac.cn`将脱敏为`ya***@sics.ac.cn`<br />* 非邮箱字符串：全部内容逐位使用星号（`*`）替换，例如`yashan`将脱敏为`******` |
| PREFIX_EMAIL_MEDIUM | 邮箱前缀脱敏（中） | CHAR、VARCHAR | 按字符集进行数据脱敏：<br />* 邮箱字符串：将邮箱前缀逐位使用星号（`*`）替换，例如`yashan@sics.ac.cn`将脱敏为`******@sics.ac.cn`、`ya@sics.ac.cn`将脱敏为`**@sics.ac.cn`<br />* 非邮箱字符串：全部内容逐位使用星号（`*`）替换，例如`yashan`将脱敏为`******` |
| PARTIAL      | 自定义部分脱敏 | CHAR、VARCHAR           | 根据指定的参数将前n位和后m位保持原状，按字符集将中间部分逐位使用星号（`*`）替换，例如参数指定为`(2,1)`会将`yashan`脱敏为`ya***n` |
| RANDOM       | 随机脱敏       | CHAR、VARCHAR           | 按字符集将全部内容逐位替换成任意可打印字符，且每次查询的结果可能不一致，例如`yashan`可能会脱敏为`9SV;1l` |
| REG_EXP      | 正则脱敏       | CHAR、VARCHAR           | 基于正则表达式匹配目标数据的特征格式，按字符集对匹配到的内容进行替换 |
| SHUFFLE      | 乱序脱敏       | CHAR、VARCHAR           | 将字符串内各个字符的位置进行随机重排，且每次查询的结果可能不一致，例如`yashan database`可能会脱敏为`eaa aasandyshbt` |



示例（HEAP表）

```plsql
-- 未创建脱敏策略时，数据直接呈现实际值
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      2022-06-17 10:45:14
0101000002                John                      2019-09-21 10:45:14
0201010011                Anna                      2024-05-17 10:45:14
0201008003                Jack                      2023-04-13 10:45:14
0201008004                Jim                       2024-08-25 10:45:14

-- 为employees表的employee_name列创建脱敏策略pol1，规则为部分脱敏（前后各保留1位）
SELECT dbms_datamask.add_rule('pol1','employees.employee_name','sales','partial','1,1') pol1 from dual;

        POL1
------------
            
-- 为employees表的entry_date列创建脱敏策略pol2，采用默认规则（日期类型替换为1970-01-01 00:00:00）
SELECT dbms_datamask.add_rule('pol2','employees.entry_date','sales','','') pol2 from dual;

        POL2
------------

-- 数据查询结果集呈现为脱敏后的内容
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00
```

## DEL_RULE

```plsql
DBMS_DATAMASK.DEL_RULE(
    POL_NAME   IN  VARCHAR);
```
DBMS_DATAMASK.DEL_RULE用于为单列数据删除脱敏策略。

示例（HEAP表）
```plsql
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                M**k                      1970-01-01 00:00:00
0101000002                J**n                      1970-01-01 00:00:00
0201010011                A**a                      1970-01-01 00:00:00
0201008003                J**k                      1970-01-01 00:00:00
0201008004                J*m                       1970-01-01 00:00:00

-- 删除脱敏策略pol1
SELECT dbms_datamask.del_rule('pol1') from dual;

DBMS_DATAMASK.DEL_RU
--------------------

-- 数据查询结果集中，employee_name列的数据呈现为原始数据
SELECT employee_no,employee_name,entry_date from employees;

EMPLOYEE_NO               EMPLOYEE_NAME             ENTRY_DATE
------------------------- ------------------------- --------------------------------
0101000001                Mask                      1970-01-01 00:00:00
0101000002                John                      1970-01-01 00:00:00
0201010011                Anna                      1970-01-01 00:00:00
0201008003                Jack                      1970-01-01 00:00:00
0201008004                Jim                       1970-01-01 00:00:00
```
