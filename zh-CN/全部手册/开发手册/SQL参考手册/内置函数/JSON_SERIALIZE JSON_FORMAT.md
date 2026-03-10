```ebnf+diagram
json_serialize::= JSON_SERIALIZE "(" json_value [returning_clause] [PRETTY] [EXTENDED] ")"

returning_clause::= RETURNING "VARCHAR(size)"
```

JSON_SERIALIZE函数将二进制[json](../通用SQL语法/json)数据按照pretty格式或compact格式序列化为字符串，且序列化后的字符串长度不超过 `returning_clause` 定义的限制值。

`json_value`为一个二进制json数据，可通过[JSON](./JSON JSON_PARSE)函数获取，当其值为NULL时，函数返回NULL。

JSON_SERIALIZE函数存在别名JSON_FORMAT，其功能与JSON_SERIALIZE完全相同。

**returning_clause**

该子句用于限制输出字符串的长度，可省略，则系统默认输出字符串的长度为65534。

size为一个整型常量，取值范围为[0,65534]。

当实际字符串长度超过size限制值时，函数返回错误。

**PRETTY**

PRETTY用于指定字符串的打印规则为pretty，不指定PRETTY时，系统默认按compact规则打印字符串。

compact即紧凑型打印规格，此规则在打印字符串时将去掉所有的规格化空格与换行。

pretty规则具体为：

1.对于JSON Array类型的数据：

- Number、String、Bool、Null类型的数组元素出现在单独的行上。
- Object或者Array类型的数组元素占多行。
- `[`和`]`各占一行，位于同一个级别，所有数组元素位于同一级别，与`[`和`]`相比，缩进了一个额外的级别，每一级别缩进为两个空格。
- 在两个数组元素的换行符前，会添加一个逗号来分割数组元素。
- 空数组仍然需要换行。

2.对于JSON Object类型的数据：

- 如果对象成员中的Value是Number、String、Bool、Null类型，key和value打印在同一行。
- 如果对象成员中的Value是Object、Array类型，key和value不在同一行输出。
- `{`和`}`各占一行，位于同一个级别，所有对象成员位于同一级别，与`{`和`}`相比，缩进了一个额外的级别，每一级别缩进为两个空格。
- 对象成员的key和value之间用"空格 冒号 空格"分割；
- 在两个对象成员的换行符前，会添加一个逗号来分割对象成员。
- 空对象仍然需要换行。

3.对于JSON String类型的数据：

- 无论是Object中的key还是String类型的标量值，打印时都会在前后使用双引号。
- 字符串本身中的'\b (backspace)'、'\f (form feed)'、'\n (newline, line feed)'、'\r (carriage return)'、'\t (tab, horizontal tab)'、'Double quotation mark (")'、'slash (/)'、'backslash (\\)'需要被转义打印；

4.对于JSON Number类型的数据：

- number的指数大于等于0时，如果展开打印后的宽度大于40，则使用科学计数法打印，否则展开打印。例如：

    - 1e39打印结果为：1000000000000000000000000000000000000000
    - 1e40打印结果为：1E+40
- number的指数小于0时，如果展开打印后的小数部分（去掉小数部分后面的0）的宽度大于40，则使用科学计数法打印，否则展开打印。例如：
    - 12300e-43打印结果为：1.23E-39（展开打印结果为0.00000000000000000000000000000000000000123，小数点后面41位，故使用科学计数法打印）
    - 12300e-42打印结果为：0.0000000000000000000000000000000000000123（小数点后面正好40位）

**EXTENDED**

EXTENDED关键字用于设置使用扩展模式输出JSON数据，详见[JSON扩展格式输出](../通用SQL语法/json)。

示例

```sql
SELECT JSON_SERIALIZE(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}') PRETTY) res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
{
  "key" : true,
  "keyA" : 234,
  "keyB" : 345,
  "keyC" : 123,
  "keyabc" : false
}

SELECT JSON_SERIALIZE(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}')) res FROM DUAL;
RES
----------------------------------------------------------------
{"key":true,"keyA":234,"keyB":345,"keyC":123,"keyabc":false}

SELECT JSON_SERIALIZE(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}') RETURNING VARCHAR(10) PRETTY) res FROM DUAL;
YAS-00215 length of concat texts exceeds the buffer limit

-- JSON扩展模式的输入输出
SELECT JSON_SERIALIZE(JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED)) res FROM DUAL ;
RES
----------------------------------------------------------------
"48656C6C6F20776F726C6421"

SELECT JSON_SERIALIZE(JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED) EXTENDED) res FROM DUAL ;
RES
----------------------------------------------------------------
{"$binary": "SGVsbG8gd29ybGQh"}

-- JSON扩展模式输出同样受到PRETTY关键字影响
SELECT JSON_SERIALIZE(JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED) PRETTY EXTENDED) res FROM DUAL ;
RES
----------------------------------------------------------------
{
  "$binary": "SGVsbG8gd29ybGQh"
}
```
