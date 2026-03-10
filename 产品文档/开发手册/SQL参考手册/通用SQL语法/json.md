JSON是一种独立于编程语言的数据交换格式，采用文本格式来表述数据，对于数据库系统，支持JSON格式可以使得应用和数据库之间传递数据变得更简洁、易懂和有结构逻辑。

YashanDB支持使用标准的JSON格式来定义和计算json数据，包括其两种结构形式和六种数据类型；还支持使用[JSON扩展格式](#json-ext)，扩展支持数据库中的其它数据类型。

关于JSON的标准语法描述详见其官方网站[ECMA-404 The JSON Data Interchange Standard](https://www.json.org/json-en.html)。

## JSON的结构形式

**键值对形式**

这种结构的json数据是一组无序的"键(Key):值(Value)"的集合，该集合通过`{`开始，以右括号`}`结束，多个"键(Key):值(Value)"之间使用`，`分割。如下所示：

```json
{
    "key1": "abc",
    "key2": 123,
    "key3": true,
    "key4": null
}
```

**数组形式**

这种结构的json数据是一组有序的值（Value）的集合，该集合通过`[`开始，以`]`结束，Value之间使用逗号`,`分割。如下所示：

```json
[
	"ghi", 
	789
]
```

**多结构嵌套混合**

一个json数据可以由多个结构形式嵌套混合组成，以满足各种业务数据组织要求。如下所示：

```json
{
    "key1": "abc",
    "key2": 123,
    "key3": true,
    "key4": null,
    "key5": [
        "def", 
        456, 
        false, 
        null, 
       	[
            "ghi", 
            789
        ], 
        {
            "key": "value"
        }
    ],
	"key6": {
        "key": "value"
    }
}
```

## JSON的数据类型

根据标准JSON格式定义，json数据里的Value可以为如下类型：

- 对象（Object）
- 数组（Array）
- 字符串（String）
- 数字（Number）
- 布尔值（True/False）
- 空值（Null）

其中：

1.对象（Object）和数组（Array）中的值（Value）可以为字符串（String）、数字（Number）、布尔值（True/False）、空值（Null）或者嵌套的对象（Object）和数组（Array）。

2.字符串（String）是由零个或者更多个Unicode字符组成的序列，前后使用双引号`"`来标识，且支持使用反斜杠`\`进行转义。

3.布尔值和空值必须采用全小写输入，即true/false/null。

<span id="json-ext" name="json-ext" class="yaslink"></span>
### JSON扩展格式

在JSON扩展格式里，json数据里的Value还可以为如下类型：

- Tinyint
- Smallint
- Integer
- Bigint
- Float
- Double
- Number
- Binary
- Timestamp
- Date
- Time

<span id="json-ext-parse" name="json-ext-parse" class="yaslink"></span>
#### JSON扩展格式解析
目前支持的所有扩展格式可以用一个仅包含一个键值对（key-value pair）来表示，格式为：`{"$key": value}`。

其中：

1. Tinyint、Smallint、Integer、Bigint、Float、Double与Number：
   - 这些类型的值需要为一个字符串，或不带引号的数值。两种情况都需要符合此类型的解析要求。
     - 对于整数类型（Tinyint、Smallint、Integer、Bigint），值需要为一个仅包含数字与正负号的字符串，不允许出现小数点或幂。
     - 对于浮点类型（Float、Double），值允许为"nan"或带正负号的"inf"（不分大小写）或者一个数值字符串。
   - 这些类型与YashanDB数据库里同名的数据类型等价，包括其类型长度与取值范围。详见[数据类型规格](../../../产品描述/产品规格/数据类型规格)。
   - 这些类型的键分别为：
     - Tinyint的键为 `$numberByte`。
     - Smallint的键为 `$numberShort`。
     - Integer的键为 `$numberInteger`。
     - Bigint的键为 `$numberLong`。
     - Float的键为 `$numberFloat`。
     - Double的键为 `$numberDouble`。
     - Number的键为 `$numberDecimal`。
2. Binary：
   - 这个类型的键为 `$binary`。
   - 这个类型的值可以是一个经过base64编码的字符串（String），也可以是有且仅有如下两个键的一个对象：
     - `base64` 对应的值为一个经过base64编码的字符串；
     - `subType` 对应的值为一个Tinyint数字，用于语法兼容，无实际意义。
   - 上述经过base64编码的字符串可以省略掉末尾的填充符 `=`。
    - 若这个字符串长度不合法（4的倍数＋1），最后一个字符将被忽略。
3. Timestamp、Date与Time：
   - Timestamp：
     - 键为 `$yashanTimestamp`。
     - 值为满足ISO-8601时间戳标准要求的字符串（不支持时区）： `yyyy-mm-ddThh24:mi:ss.ff`。
       - 根据标准，字母 `T` 或者一个空格都可以匹配 `T`。
       - 根据标准，小数点 `.` 或者逗号 `,` 都可以匹配 `.`。
      - 其他日期格式详见[日期时间型](../数据类型（yashan模式）/日期时间型)。
    - Date：
      - 键为 `$yashanDate`。
      - 值为满足ISO-8601日期格式要求的字符串（不支持时区）： `yyyy-mm-ddThh24:mi:ss.ff`。
        - 由于Date精度为秒，毫秒数据（`.ff`，最高支持9位小数）将被忽略。
        - 其他格式与Timestamp相同。
    - Time:
      - 键为 `$yashanTime`。
      - 值为满足ISO-8601时间格式要求的字符串： `Thh24:mi:ss.ff`。

在将一个对象与支持的扩展格式进行匹配时，仅当匹配完全成功时，此对象才会被解析成其对应的扩展格式数据，否则它仍然会被解析成一个对象。

常见的匹配错误包括：

- 在匹配整数类型时（Tinyint、Smallint、Integer、Bigint）：
  - 输入值包含不合法字符。
  - 输入值超出此类型可以表达的范围。
- 在匹配Number类型时，输入值为无限或非数字（NaN）；注意Number类型不支持此类值，但是Float和Double类型支持。
- 在匹配Binary类型时，输入的base64编码字符串不符合其编码要求，例如 `{"$binary": "«»"}`。
- 输入的字符串不满足解析要求的其他情况。

当处于扩展模式时，JSON函数还会进一步处理未显式指示类型的数值数据：
1. 若一个数值是整数，尝试将其用更小的数据格式表示，按Tinyint < Smallint < Integer < Bigint依次尝试。对于每个数据类型，若此数字在其能够表达的数值区间内，则将这个数值的数据类型解析成此数据类型。
2. 若一个数值是小数，将其解析为Double类型。
3. 在其他情况下，此数值仍将会以Number类型保存。

示例
```sql
SELECT JSON('{"$numberByte": "-128"}' EXTENDED) byte,
       JSON('{"$numberByte": "-129"}' EXTENDED) obj FROM DUAL;

BYTE                                                             OBJ
---------------------------------------------------------------- ----------------------------------------------------------------
-128                                                             {"$numberByte":"-129"}

SELECT JSON_QUERY(JSON('1'), '$.type()' WITH ARRAY WRAPPER) std,
       JSON_QUERY(JSON('1' EXTENDED), '$.type()' WITH ARRAY WRAPPER) ext FROM DUAL ;

STD                                                              EXT
---------------------------------------------------------------- ----------------------------------------------------------------
["number"]                                                       ["tinyint"]

SELECT JSON_QUERY(JSON('0.1'), '$.type()' WITH ARRAY WRAPPER) std,
       JSON_QUERY(JSON('0.1' EXTENDED), '$.type()' WITH ARRAY WRAPPER) ext FROM DUAL ;

STD                                                              EXT
---------------------------------------------------------------- ----------------------------------------------------------------
["number"]                                                       ["double"]
```

<span id="json-ext-print" name="json-ext-print" class="yaslink"></span>
#### JSON扩展格式输出

JSON数据仅在通过使用 `JSON_SERIALIZE()` 函数的 `EXTENDED` 关键字时使用扩展模式进行输出。

其他情况下（例如客户端输出，或者调用不包含  `EXTENDED` 关键字的 `JSON_SERIALIZE()` 函数等）都会使用标准模式进行输出。

在标准格式时，若JSON数据包含扩展类型，根据以下规则输出：

1. 当数据为Tinyint，Smallint，Integer，Bigint，Number时，直接输出其数值，输出规则与YashanDB中同名的类型的输出规则相同。
2. 当数据为Float与Double时：
   - 若此数值为有限数值，直接输出其数值，其中Float精度为9位有效数字，Double精度为17位有效数字。
    - 若此数值不为有限数值（NaN以及正负无穷），输出首字母大写的对应字符串：`"Nan"`，`"Inf"`，`"-Inf"`。
3. 当数据为Binary类型时，输出结果为一个代表此二进制数据的十六进制字符串。
4. 当数据为时间类型（Timestamp，Date，Time）时，输出结果为一个代表此时间类型的，符合ISO-8601格式的字符串。
   - Timestamp类型使用 `"yyyy-mm-ddThh24:mi:ss.ff"` 格式输出，其中 `T` 固定为字母T，精确度为毫秒，且小数点以及毫秒仅在不为0时输出。
    - Date类型使用 `"yyyy-mm-ddThh24:mi:ss"` 格式输出，其中 `T` 固定为字母T，精确度为秒。
    - Time类型使用 `"hh24:mi:ss.ff"` 格式输出，精确度为毫秒，且小数点以及毫秒仅在不为0时输出。

在扩展格式时，根据以下规则输出。为方便叙述，当说明输出是一个拥有唯一一个键值对的对象时，描述为输出“键”格式的对象，输出值为此键对应的值，例如 `{"键": 值}`。

1. 当数据为小整数类型（Tinyint，Smallint，Integer）类型时，直接输出其数值，输出规则与标准模式相同。
2. 当数据为Bigint或Number类型，且为不超过 `(-2^53, +2^53)` 区间的整数时，直接输出其数值。
3. 当数据为Bigint或Number类型，且为超过上述区间但不超过Bigint区间的整数时，输出 `"$numberLong"` 格式对象，值为其数值的字符串。
4. 当数据为Number类型且不满足上述条件时，输出 `"$numberDecimal"` 格式对象，值为其数值的字符串。
5. 当数据为Float类型时，输出 `"$numberFloat"` 格式对象：
   - 若为有限数值，值为其数值的字符串。
    - 若不为有限数值，值为其在标准格式下输出的字符串。
6. 当数据为Double类型时：
   - 若为有限数值，直接输出其数值，输出规则与标准模式相同。
    - 若不为有限数值，输出 `"$numberDouble"` 格式对象，值为其在标准格式下输出的字符串。
7. 当数据为Binary类型时，输出 `"$binary"` 格式对象，值为其二进制数据对应的base64编码。
8. 当数据为时间（Timestamp，Date，Time）类型时，输出扩展格式对象，值为其在标准格式下输出的字符串。
   - Timestamp类型的键为 `$yashanTimestamp`。
    - Date类型的键为 `$yashanDate`。
    - Time类型的键为 `$yashanTime`。
## 路径表达式

YashanDB提供了一些内置函数实现对一个json数据内部的搜索查找功能（例如检索某个Key是否存在于某个json数据中），这需要结合路径表达式（Path Expression）一起使用。

路径表达式有点类似于XML数据的XQuery或XPath表达式，其语法定义如下：

**[json_path](#jsonpath)::=**

```ebnf+diagram
syntax::= "$" [nonfunction_steps] [function_step]
```

**[nonfunction_steps](#nonfunctionsteps)::=**

```ebnf+diagram
syntax::= [object_step] [array_step] [descendent_step]
```

**[object_step](#objectstep)::=**

```ebnf+diagram
syntax::= "." ("*"|key)
```

**[array_step](#arraystep)::=**

```ebnf+diagram
syntax::= "[" ("*"|((array_index|(array_n "to" array_m)) {"," (array_index|(array_n "to" array_m))})) "]"
```

**[descendent_step](#descendentstep)::=**

```ebnf+diagram
syntax::= ".." key
```

**[function_step](#functionstep)::=**

```ebnf+diagram
syntax::= "." item_method "()"
```

**[item_method](#itemmethod)::=**

```ebnf+diagram
syntax::= "count"|"size"|"type"
```

<span id="jsonpath" name="jsonpath" class="yaslink"></span>

路径表达式使用绝对路径，以符号`$`开始，其含义用于表述检索json数据时的匹配路径。`$`后面跟着零个或者多个nonfunction_steps，以及一个可选的function_step，即支持多步骤匹配；不跟任何内容时，表示与json数据完全一致的匹配路径。

一个路径表达式会选择零个或者多个匹配的值。一般情况下，路径表达式会依次尝试匹配路径表达式中的每一个步骤。如果当前步骤匹配失败，则不会尝试匹配后续步骤，并且路径表达式匹配失败。如果每一个步骤都匹配成功，则路径表达式匹配成功。

其中路径表达式中的key在无双引号的情况下只允许字母数字，不允许任何其余字符出现，需要使用特殊字符时需要按标准json string语法在字符串的前后加上双引号。

示例

```sql
SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$') res FROM dual;
RES                                                              
---------------------------------------------------------------- 
{"1":1,"2":2,"3":3} 
```

<span id="nonfunctionsteps" name="nonfunctionsteps" class="yaslink"></span>

### nonfunction\_steps

非函数类匹配步骤，可以为object_step、array_step或者descendent_step中的某一个。

<span id="objectstep" name="objectstep" class="yaslink"></span>

#### object\_step

针对键值对结构的json数据的匹配步骤，以`.`开始，后面跟着一个Key或者一个通配符`*`。

Key如果不使用双引号标识时，必须以大写或者小写字母a-z开头，且只能包含字母或者十进制数字（0-9），否则必须使用双引号进行标识。

Key可以为空，如果为空，则必须写成`""`的形式。

使用object_step匹配时，系统将按指定的Key检索json数据，并返回Key对应的Value。指定通配符时，返回所有的Value。

示例

```sql
SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$.*' WITH WRAPPER) res FROM dual;
RES                                                              
---------------------------------------------------------------- 
[1,2,3]   

SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$.""') res FROM dual;
RES                                                              
---------------------------------------------------------------- 
   
SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$."1"') res FROM dual;   
RES                                                              
---------------------------------------------------------------- 
1   
```

<span id="arraystep" name="arraystep" class="yaslink"></span>

#### array\_step

针对数组结构的json数据的匹配步骤，以`[`开始，以`]`结束，不允许出现空的[]，[]中间的内容形式可以为：

- 通配符`*`：匹配所有Value。
- 数组索引位置array_index：按Value在数组中的位置匹配，可以同时指定多个位置匹配，以`,`分隔。数组位置为从0开始的整数，即数组的第一个元素的索引为0。也可以是`last - N`的形式，表示从数组尾部开始查找，例如last - 1表示索引数组中倒数第二个元素。单独的`last`等价于`last - 0`，表示索引数组中最后一个元素。
- 数组索引范围array_n "to" array_m：其形式为`N to M`，N和M都是位置索引，按Value在数组中的位置范围进行匹配，等价于显式地指定从N到M的所有索引位置，例如[2 to 4]等价于[2, 3, 4]。其中N和M的大小顺序没有特殊要求，例如，表示第三到第六个元素的索引范围可以是[2 to 5]，也可以是[5 to 2]。需要注意的是`[N to N]`等价于`[N]`而不是`[N, N]`。

使用array_step匹配时，上述三种形式必须至少出现一个，但是通配符只能独立出现。

当数组索引位置和数组索引范围可以出现多个，当多个索引位置存在重叠时，重叠区域中的元素会被多次匹配到。

如果指定的索引位置或者索引范围超过了数组本身的范围，则系统将认为没有匹配到数据，而不会报错。

示例

```sql
SELECT JSON_QUERY(JSON('[1,2,3,4,5,6,7,8,9]'), '$[0 to 2, 5 to 3, last to last - 2, 1, 1]' WITH WRAPPER) res FROM dual;
RES                                                              
---------------------------------------------------------------- 
[1,2,3,4,5,6,7,8,9,2,2]  
```

<span id="descendentstep" name="descendentstep" class="yaslink"></span>

#### descendent\_step

针对键值对结构的json数据的递归匹配步骤，以两个连续的句号`..`开始，后面跟着一个Key。它基于当前步骤之前匹配到的数据，进行递归查找，返回所有匹配到的值。

示例

```sql
SELECT JSON_QUERY(JSON('{ "a" : { "b" : { "d" : 1 },
          						  "c" : [ 2, { "d" : 3 } ],
         						  "d" : 4 },
						  "d" : 5 }'), '$.a..d' WITH WRAPPER) res FROM dual;
RES                                                              
---------------------------------------------------------------- 
[4,1,3] 
```

<span id="functionstep" name="functionstep" class="yaslink"></span>

### function\_step

函数类匹配步骤，function_step在路径表达式中不是必须出现的，如果出现，它只能是路径表达式的最后一步。function_step以`.`开始，后面跟着函数方法名称，及左右括号`()`，括号之间可以有空格。

<span id="itemmethod" name="itemmethod" class="yaslink"></span>

#### item\_method

函数方法名称，函数方法应用于当前步骤之前匹配到的数据，对其进行数据转换，包括如下几种：

- count：返回当前步骤之前匹配到的数据的数量。
- size：如果当前步骤之前匹配到的数据为数组，返回数组的元素数量，如果不是数组，返回NULL。
- type：返回当前步骤之前匹配到的数据的类型。

示例

```sql
SELECT JSON_QUERY(JSON('{ "a" : { "b" : { "d" : 1 },
          						  "c" : [ 2, { "d" : 3 } ],
         						  "d" : 4 },
						  "d" : 5 }'), '$.a..d.count()' WITH WRAPPER) res FROM dual;
RES                                                              
---------------------------------------------------------------- 
[3]
```

### 宽松匹配

在严格匹配的模式下，根据路径表达式对json数据进行检索时，路径表达式中的某个匹配步骤只能应用于特定的数据类型，具体要求为：

- object_step只能对Object类型数据进行查找，应用于其他类型均认为匹配失败。
- array_step只能对Array类型数据进行查找，应用于其他类型均认为匹配失败。

为了提供更灵活的查询，YashanDB支持宽松匹配模式（系统默认为此种模式），具体规则为：

- 如果当前为array_step匹配，但json数据为非Array类型，则系统自动将json数据封装为长度为1的数组，然后进行匹配。

  示例

  ```sql
  SELECT JSON_QUERY(JSON('1'), '$[0]') res FROM dual;
  RES                                                              
  ---------------------------------------------------------------- 
  1   
  ```

- 如果当前为非array_step匹配，如object_step、function_step等，但json数据为Array类型，则系统对Array中的每个元素应用当前的匹配路径。

  示例
  
  ```sql
  SELECT JSON_QUERY(JSON('[{"a": 1}, {"a": 2}, {"a": 3}]'), '$.a' WITH WRAPPER) res FROM dual;
  RES                                                              
  ---------------------------------------------------------------- 
  [1,2,3] 
  ```

