
YashanDB的正则表达式实现兼容与Perl相同的语法和语义，此外也支持Unicode正则表达式规范。

> **Note**: 
> “UTF8支持”表示：当数据库服务端字符集为UTF8时，对于除ASCII外的UTF8字符（如中文等）的支持情况。
> 此外，若数据库服务端采用其他编码格式，其匹配规则都遵循ASCII编码字符匹配规则。
> 以下规则对于ASCII编码字符均适用。

## 元字符：

### 表1-1：元字符（非出现在字符类中）

|  符号| 描述| UTF8支持|
|---             |---                                                             |---      |
|\               |通用转义字符（有多种用途）                                        |支持     |
|^               |字符串（或行，在多行模式下）的开始                                |支持     |
|$               |字符串（或行，在多行模式下）的结束                                |支持     |
|.               |匹配除换行符以外的任何字符（默认）                                |支持     |
|[               |字符类开始标志                                                  |支持     |
|]               |字符类结束标志                                                  |支持     |
|&#124;          |备用分支的启动                                                  |支持     |
|(               |子模式或控件谓词开始标志                                        |支持     |
|)               |子模式或控件谓词结束标志                                        |支持     |
|*               |出现0次或多次（greedy）                                           |支持     |
|+               |出现1次或多次（possessive）                                       |支持     |
|?               |出现0次或1次（lazy）                                              |支持     |
|{               |重复引用符的开始标志                                            |支持     |
|}               |重复引用符的结束标志                                            |支持     |

### 表1-2：元字符（出现在字符类中）

|  符号| 描述| UTF8支持|
|---             |---                                                             |---      |
|\               |通用的转义字符                                                  |支持     |
|^               |该类取反，但只对第一个字符生效                                  |支持     |
|-               |指示字符范围                                                    |支持     |
|[               |POSIX字符类开始标志（如果后面跟着POSIX语法）                      |支持     |
|]               |POSIX字符类结束标志                                             |支持     |

## 匹配方式：

### 表2-1：单一匹配

|  符号| 描述| UTF8支持|
|---                 |---                                                                      |---      |
|.                   |匹配非换行符的任何字符                                                   |支持     |
|x                   |单个字符x                                                                |支持     |
|&#91;字符类&#93;    |匹配“字符类”中的一个字符，“字符类”见后面的说明                           |支持     |
|&#91;^字符类&#93;   |匹配“字符类”外的一个字符，“字符类”见后面的说明                           |支持     |
|(xxx)               |匹配xxx，将内部表达式标记为子表达式                                      |支持     |
|\小写字母Perl标记   |匹配“Perl字符类”中的一个字符，“Perl字符类”见后面的说明                   |支持     |
|\大写字母Perl标记   |匹配“Perl字符类”外的一个字符，“Perl字符类”见后面的说明                   |支持     |
|\p{xx}              |匹配“Unicode类“（普通类+脚本类）中的一个字符，“Unicode类”见后面的说明      |支持     |
|\P{xx}              |匹配“Unicode类“（普通类+脚本类）外的一个字符，“Unicode类”见后面的说明      |支持     |
|\pxx                |匹配“Unicode类“（仅普通类）中的一个字符，“Unicode类”见后面的说明           |支持     |
|\Pxx                |匹配“Unicode类“（仅普通类）外的一个字符，“Unicode类”见后面的说明           |支持     |

### 表2-2：复合匹配

|  符号| 描述| UTF8支持|
|---             |---                                                          |---      |
|xy              |匹配xy（x后面跟随y）                                          |支持     |
|x&#124;y        |匹配x或y（优先匹配x）                                         |支持     |

### 表2-3：重复匹配

|  符号| 描述| UTF8支持|
| ------- | ----------------------------------------- | -------- |
| x?      | x，零次或一次（greedy）                     | 支持     |
| x?+     | x，零次或一次（possessive）                 | 支持     |
| x??     | x，零次或一次（lazy）                       | 支持     |
| x*      | x，零次或多次（greedy）                     | 支持     |
| x*+     | x，零次或多次（possessive）                 | 支持     |
| x*?     | x，零次或多次（lazy）                       | 支持     |
| x+      | x，一次或多次（greedy）                     | 支持     |
| x++     | x，一次或多次（possessive）                 | 支持     |
| x+?     | x，一次或多次（lazy）                       | 支持     |
| x{n}    | x，恰好n次（greedy）                      | 支持     |
| x{n,m}  | x，至少n次，但是不超过m次（greedy）     | 支持     |
| x{n,m}+ | x，至少n次，但是不超过m次（possessive） | 支持     |
| x{n,m}? | x，至少n次，但是不超过m次（lazy）       | 支持     |
| x{n,}   | x，至少n次（greedy）                      | 支持     |
| x{n,}+  | x，至少n次（possessive）                  | 支持     |
| x{n,}?  | x，至少n次（lazy）                        | 支持     |

## 匹配位置：

### 表3-1：匹配位置

指定一个条件必须在一个特定的点匹配，不消耗任何字符的字符串。

|  符号| 描述| UTF8支持|
|---             |---                                                          |---      |
|^               |匹配字符串开始                                               |--       |
|$               |匹配字符串结束                                               |--       |
|\b              |匹配单词边界                                                 |--       |
|\B              |匹配非单词边界                                               |--       |
|\A              |匹配字符串开始                                               |--       |
|\Z              |匹配字符串结束，也匹配字符串结尾的换行符之前                  |--       |
|\z              |匹配字符串结束                                               |--       |
|\G              |匹配字符串中的第一个匹配位置                                 |--       |

## 转义字符：

### 表4-1：转义字符

|  转义字符| 描述| UTF8支持|
|---        |---                              |---      |
|\a         |alarm字符（hex 07）                |--       |
|\cx        |控制字符                         |--       |
|\d         |反向引用表达式（d是介于1和9之间的阿拉伯数字），匹配出现在'('和')'之间的子表达式 |--       |
|\e         |escape字符（hex 1B）               |--       |
|\f         |换页字符（hex 0C）                 |--       |
|\n         |换行字符（hex 0A）                 |--       |
|\r         |回车字符（hex 0D）                 |--       |
|\t         |制表字符（hex 09）                 |--       |
|\0dd       |八进制数dd                       |--       |
|\ddd       |八进制数ddd（或者反向引用的序号）  |--       |
|\o{ddd..}  |八进制数ddd..                    |--       |
|\xhh       |十六进制数hh                     |--       |
|\x{hh..}   |十六进制数hh..                   |--       |
|\\\\       |字符\                           |--       |
|\\^        |字符^                           |--       |
|\\$        |字符$                           |--       |
|\\.        |字符.                           |--       |
|\\*        |字符*                           |--       |
|\\+        |字符+                           |--       |
|\\?        |字符?                           |--       |
|\\{        |字符{                           |--       |
|\\}        |字符}                           |--       |
|\\(        |字符(                           |--       |
|\\)        |字符)                           |--       |
|\\[        |字符[                           |--       |
|\\]        |字符]                           |--       |
|\\&#124;   |字符&#124;                      |--       |

## 字符类：

### 表5-1：字符类汇总

一般字符类的使用方式为[...]，POSIX字符类的使用方式为[\[:xxx:]]

|  符号| 描述| UTF8支持|
|---             |---                                                          |---      |
|[...]           |指定与列表中表示的任何表达式匹配的字符（字符之间是"或"的意思，例如[12]匹配1或2）    |支持     |
|&#91;^...&#93;  |指定与列表外表示的任何表达式匹配的字符                       |支持     |
|[x-y]           |范围（可用于十六进制字符，包含首尾字符）                        |支持     |
|\[[:xxx:]]      |POSIX字符类                                                  |--       |
|\[[:^xxx:]]     |非POSIX字符类                                                |--       |
|[\字母]         |Perl字符类                                                   |支持     |
|[p{xx}]         |Unicode类（普通类+脚本类）                                     |支持     |
|[pxx]           |Unicode类（仅普通类）                                          |支持     |

### 表5-2：POSIX字符类

|  POSIX字符类| 描述| UTF8支持|
| ----------- | -------------------------------------------- | -------- |
| alnum       | 字母数字（相当于[0-9A-Za-z]）                | --       |
| alpha       | 字母（相当于[A-Za-z]）                       | --       |
| ascii       | ASCII字符集（相当于[\x00-\x7F]）            | --       |
| blank       | 空白占位符（相当于[\t]）                     | --       |
| cntrl       | 控制字符（相当于[\x00-\x1F\x7F]）            | --       |
| digit       | 数字（相当于[0-9]）                          | --       |
| graph       | 图形字符（相当于[!-~]）                      | --       |
| lower       | 小写字母（相当于[a-z]）                      | --       |
| print       | 可打印字符（相当于[-~]，相当于[\[:graph:]]） | --       |
| punct       | 标点符号（相当于[!-/`:-@`[-`{-~]）           | --       |
| space       | 空白字符（相当于[\t\n\v\f\r]）                | --       |
| upper       | 大写字母（相当于[A-Z]）                       | --       |
| word        | 单词字符（相当于[0-9A-Za-z]）                 | --       |
| xdigit      | 16进制字符（相当于[0-9A-Fa-f]）               | --       |

### 表5-3：Perl字符类

|  Perl字符类| 描述| UTF8支持|
|---        |---                                              |---      |
|\C         |一个代码单元，建议尽量避免（即使在UTF-8模式下）     |--       |
|\d         |十进制数字（相当于[0-9]）                         |--       |
|\D         |非十进制数字                                     |支持     |
|\h         |水平空白字符                                     |--       |
|\H         |非水平空白字符                                   |支持     |
|\N         |非换行符                                         |支持     |
|\s         |空格符（相当于[\t\n\f\r ]）                       |--       |
|\S         |非空格符                                         |支持     |
|\v         |垂直空白字符                                     |--       |
|\V         |非垂直空白字符                                   |支持     |
|\w         |单词字符（相当于[0-9A-Za-z]）                    |--       |
|\W         |非单词字符                                       |支持     |
|\R         |换行符                                           |--       |
|\X         |Unicode扩展的字母簇                              |支持     |

### 表5-4：Unicode类（普通类）

|  \p和\P属性| 描述| UTF8支持|
|---        |---                                      |---      |
|C          |其他（Other）                              |支持     |
|Cc         |控制字符（Control）                        |支持     |
|Cf         |格式字符（Format）                         |支持     |
|Cn         |未赋值字符（Unassigned）                   |支持     |
|Co         |私人使用区（Private use）                  |支持     |
|Cs         |代理区（Surrogate）                        |支持     |
|L          |字母字符（Letter）                         |支持     |
|Ll         |小写字母（Lower case letter）              |支持     |
|Lm         |修饰字母（Modifier letter）                |支持     |
|Lo         |其他字母（Other letter）                   |支持     |
|Lt         |首字母大写字母（Title case letter）        |支持     |
|Lu         |大写字母（Upper case letter）              |支持     |
|L&         |Ll、Lu或Lt                          |支持     |
|M          |标记（Mark）                               |支持     |
|Mc         |间距标记（Spacing mark）                   |支持     |
|Me         |嵌入标记（Enclosing mark）                 |支持     |
|Mn         |非间距标记（Non-spacing mark）             |支持     |
|N          |数字（Number）                             |支持     |
|Nd         |十进制数字（Decimal number）               |支持     |
|Nl         |字母数字（Letter number）                  |支持     |
|No         |其他数字（Other number）                   |支持     |
|P          |标点（Punctuation）                        |支持     |
|Pc         |连接标点（Connector punctuation）          |支持     |
|Pd         |破折号（Dash punctuation）                 |支持     |
|Pe         |封闭标点，指括号（Close punctuation）      |支持     |
|Pf         |最后的标点（Final punctuation）            |支持     |
|Pi         |最初的标点（Initial punctuation）          |支持     |
|Po         |其他标点（Other punctuation）              |支持     |
|Ps         |开放的标点（Open punctuation）             |支持     |
|S          |符号（Symbol）                             |支持     |
|Sc         |货币符号（Currency symbol）                |支持     |
|Sk         |修饰符号（Modifier symbol）                |支持     |
|Sm         |数学符号（Mathematical symbol）            |支持     |
|So         |其他符号（Other symbol）                   |支持     |
|Xan        |属性L和N的并集                           |支持     |
|Xps        |POSIX space：属性Z或tab、NL、VT、FF、CR  |支持     |
|Xsp        |Perl space：属性Z或tab、NL、VT、FF、CR   |支持     |
|Xuc        |统一命名字符：可以由通用字符名称表示     |支持     |
|Xwd        |Perl word：属性Xan或下划线               |支持     |
|Z          |分隔符（Separator）                        |支持     |
|Zl         |行分隔符（Line separator）                 |支持     |
|Zp         |段落分隔符（Paragraph separator）          |支持     |
|Zs         |空白分隔符（Space separator）              |支持     |

### 表5-5：Unicode类（脚本类）

注：支持UTF8。

Adlam, Ahom, Anatolian_Hieroglyphs, Arabic, Armenian, Avestan, 
Balinese, Bamum, Bassa_Vah, Batak, Bengali, Bhaiksuki, Bopomofo, Brahmi, Braille, Buginese, Buhid, 
Canadian_Aboriginal, Carian, Caucasian_Albanian, Chakma, Cham, Cherokee, Chorasmian, Common, Coptic, Cuneiform, Cypriot, Cypro_Minoan, Cyrillic, 
Deseret, Devanagari, Dives_Akuru, Dogra, Duployan, 
Egyptian_Hieroglyphs, Elbasan, Elymaic, Ethiopic, 
Georgian, Glagolitic, Gothic, Grantha, Greek, Gujarati, Gunjala_Gondi, Gurmukhi, 
Han, Hangul, Hanifi_Rohingya, Hanunoo, Hatran, Hebrew, Hiragana, 
Imperial_Aramaic, Inherited, Inscriptional_Pahlavi, Inscriptional_Parthian, 
Javanese, 
Kaithi, Kannada, Katakana, Kayah_Li, Kharoshthi, Khitan_Small_Script, Khmer, Khojki, Khudawadi, 
Lao, Latin, Lepcha, Limbu, Linear_A, Linear_B, Lisu, Lycian, Lydian, 
Mahajani, Makasar, Malayalam, Mandaic, Manichaean, Marchen, Masaram_Gondi, Medefaidrin, Meetei_Mayek, Mende_Kikakui, Meroitic_Cursive, Meroitic_Hieroglyphs, Miao, Modi, Mongolian, Mro, Multani, Myanmar, 
Nabataean, Nandinagari, New_Tai_Lue, Newa, Nko, Nushu, Nyakeng_Puachue_Hmong, 
Ogham, Ol_Chiki, Old_Hungarian, Old_Italic, Old_North_Arabian, Old_Permic, Old_Persian, Old_Sogdian, Old_South_Arabian, Old_Turkic, Old_Uyghur, Oriya, Osage, Osmanya, 
Pahawh_Hmong, Palmyrene, Pau_Cin_Hau, Phags_Pa, Phoenician, Psalter_Pahlavi, 
Rejang, Runic, 
Samaritan, Saurashtra, Sharada, Shavian, Siddham, SignWriting, Sinhala, Sogdian, Sora_Sompeng, Soyombo, Sundanese, Syloti_Nagri, Syriac, 
Tagalog, Tagbanwa, Tai_Le, Tai_Tham, Tai_Viet, Takri, Tamil, Tangsa, Tangut, Telugu, Thaana, Thai, Tibetan, Tifinagh, Tirhuta, Toto, 
Ugaritic, 
Vai, Vithkuqi, 
Wancho, Warang_Citi, 
Yezidi, Yi, 
Zanabazar_Square.

## 匹配控制类：

### 表6-1：匹配规则控制类

这些匹配控制选项仅生效于单次匹配，匹配结束会自动取消。

这些匹配选项的优先级高于regexp_like、regexp_count、regexp_instr、regexp_substr、regexp_replace函数中match_param表达式设置的相应属性，例如：

- `SELECT regexp_like('aa','(?i)AA','c') FROM DUAL; `返回TRUE
- `SELECT regexp_like('aa','AA','c') FROM DUAL;` 返回FALSE
- pattern中的`(?i)`的优先级高于match_param中的`c`

同时，匹配控制选项生效于后面模式的其余部分，举例：`(a(?i)b)c`

以上模式匹配abc或aBc，通过这种方式，可以使选项在模式的不同部分具有不同的设置。

|  匹配规则控制类| 描述|
|---            |---                                 |
|(?i)           |大小写不敏感                        |
|(?J)           |允许重复命名组                      |
|(?m)           |多行模式                            |
|(?n)           |禁用自动捕获                        |
|(?s)           |.同时匹配换行符                     |
|(?U)           |默认匹配模式改为lazy                |
|(?x)           |扩展：忽略除类之外的空白             |
|(?xx)          |同(?x)，但也忽略了类中的空格和TAB   |
|(?-...)        |取消设置                            |
|(?^)           |取消imnsx选项                       |

### 表6-2：匹配资源控制类

以下选项仅在模式的最开始处或使用类似语法的换行符或选项之一之后才能识别。可能会出现多个。

对于前三个，d是一个十进制数。

|  匹配资源控制类| 描述|
| -------------------- | ------------------------------------------------------------ |
| (*LIMIT_DEPTH=d)     | 将回溯限制设置为d层（匹配使用系统栈进行回溯，此设置可以限制系统栈回溯层数） |
| (*LIMIT_HEAP=d)      | 将堆大小限制设置为d*1024字节（默认20000000）                   |
| (*LIMIT_MATCH=d)     | 将匹配限制设置为d（默认10000000）                              |
| (*NOTEMPTY)          | 模式不匹配空串（举例：模式a?b?匹配主题开头的空字符串，在设置该选项后无效） |
| (*NOTEMPTY_ATSTART)  | 规则基本同上，不同的是它仅在第一个匹配位置锁定空字符串匹配   |
| (*NO_AUTO_POSSESS)   | 禁用“自动占有”，这是一种优化，例如，将a+b转换为a++b，以避免回溯到永远不会成功的a+。如果希望匹配函数执行完整的未优化搜索并运行所有标注，则可以设置此选项，但它主要用于测试目的。不建议使用。 |
| (*NO_DOTSTAR_ANCHOR) | 禁用.\*锚定优化，使用建议同(\*NO_AUTO_POSSESS)，不建议使用。 |
| (*NO_JIT)            | 禁用JIT优化（YashanDB正则表达式引擎不支持JIT模式，该设置将失效） |
| (*NO_START_OPT)      | 禁用没有开始匹配优化，使用建议同(*NO_AUTO_POSSESS)，不建议使用。 |
| (*UTF)               | 为正在使用的匹配设置UTF模式                                  |
| (*UCP)               | 这个选项有两个效果。首先，它改变了PCRE2处理\B、\b、\D、\d、\S、\s、\W、\w和一些POSIX字符类的方式。默认情况下，只能识别ASCII字符，但如果设置PCRE2_UCP，则使用Unicode属性对字符进行分类。如果设置PCRE2_UCP，匹配它影响的项目需要更长的时间。PCRE2_UCP的第二个影响是强制对代码点大于127的字符使用Unicode属性进行上/下大小写操作，即使未设置PCRE2_UTF。 |

### 表6-3：换行符控制类

这些换行符控制类将改变匹配过程中对换行符的识别规则，默认换行符为LF，即hex 0A。

以下选项仅在模式的最开始处或使用类似语法的换行符或选项之一之后才能识别。仅限一个。

newline约定的选择不会影响\n或\r转义符的解释，也不会影响\R匹配的内容。这些有自己的独立约定。

|  换行符控制类| 描述|
|---                  |---                                                                                  |
|(*CR)                |仅回车符（hex 0D）                                                                     |
|(*LF)                |仅换行符（hex 0A）（默认）                                                               |
|(*CRLF)              |回车符+换行符（hex 0D + hex 0A）                                                       |
|(*ANYCRLF)           |上边三项任其一                                                                       |
|(*ANY)               |任何Unicode newline序列                                                              |
|(*NUL)               |NUL字符（二进制零）                                                                  |
|(*BSR_ANYCRLF)       |CR、LF或CRLF                                                                       |
|(*BSR_UNICODE)       |任何Unicode newline序列                                                              |

## 组类：

### 表7-1：捕获组类

捕获组通常与反向引用和搭配使用，例如：
1. 模式(?\<leader>king|queen)\g{leader}匹配字符串kingking。
    命名组leader匹配到king，之后\g{leader}反向引用匹配到的king。
2. 模式(?\<leader>king|queen)\g{leader}不匹配字符串kingqueen。

捕获组的序号按照模式中左括号的先后顺序排列。

注：在非UTF模式下，名称可能包含下划线、ASCII字母和数字；在UTF模式下，允许使用任何Unicode字母和Unicode十进制数字。
在这两种情况下，名称不得以数字开头。

|  捕获组类| 描述|
|---                  |---                                                                                  |
|(...)                |捕获组                                                                               |
|(?\<name>...)         |命名捕获组（Perl）                                                                   |
|(?'name'...)         |命名捕获组（Perl）                                                                   |
|(?P\<name>...)        |命名捕获组（Python）                                                                 |
|(?:...)              |非捕获组                                                                             |
|(?&#124;...)              |非捕获组；重置的组号，捕获每个备选方案中的组                                         |

### 表7-2：原子组类

|  原子组类| 描述|
|---                  |---                                                                                  |
|(?>...)              |原子非捕获组                                                                         |
|(*atomic:...)        |原子非捕获组                                                                         |

### 表7-3：注释组类

|  注释组类| 描述|
|---                  |---                                                                                  |
|(?#....)             |注释（不可嵌套）                                                                       |
|#....                |注释（仅生效于match_param设定包含"x"的情况下）                                         |

## 环视断言：

### 表8-1：前视和后视断言

前视断言可以控制匹配返回结果只包含匹配项的部分内容，其他断言类似，例如：
调用regexp_substr函数匹配字符串beijing_2022，可以使用含有前视断言的模式"beijing(?=_[1-9]*)"使其只返回beijing。

积极前视断言、消极前视断言、积极后视断言、消极后视断言使用举例：

1. 模式 `\w(?=;)`匹配任何单词后跟一个分号，但不包含这个分号
2. 模式 `\w(?!=;)`匹配任何单词后跟一个非分号，但不包含这个非分号
3. 模式 `(?<=;)\w`匹配分号后跟任何单词，但不包含这个单词
4. 模式 `(?<!;)\w`匹配分号后跟任何非单词，但不包含这个非单词

注：后视断言的每个顶级分支必须具有固定的长度。

|  断言| 描述|
|---                        |---                                                                            |
|(?=...)                    |积极前视断言                                                                   |
|(*pla:...)                 |积极前视断言                                                                   |
|(*positive_lookahead:...)  |积极前视断言                                                                   |
|(?!...)                    |消极前视断言                                                                   |
|(*nla:...)                 |消极前视断言                                                                   |
|(*negative_lookahead:...)  |消极前视断言                                                                   |
|(?<=...)                   |积极后视断言                                                                   |
|(*plb:...)                 |积极后视断言                                                                   |
|(*positive_lookbehind:...) |积极后视断言                                                                   |
|(?<!...)                   |消极后视断言                                                                   |
|(*nlb:...)                 |消极后视断言                                                                   |
|(*negative_lookbehind:...) |消极后视断言                                                                   |

### 表8-2：非原子环视断言

|  断言| 描述|
|---                                    |---                                                                |
|(?*...)                                |非原子积极前视断言                                                 |
|(*napla:...)                           |非原子积极前视断言                                                 |
|(*non_atomic_positive_lookahead:...)   |非原子积极前视断言                                                 |
|(?<*...)                               |非原子积极后视断言                                                 |
|(*naplb:...)                           |非原子积极后视断言                                                 |
|(*non_atomic_positive_lookbehind:...)  |非原子积极后视断言                                                 |

## 运行脚本：

### 表9-1：运行脚本类

脚本类是一系列字符，这些字符都来自相同的Unicode脚本，如拉丁语或希腊语。
使用脚本类可用于检测看起来相同但来自不同脚本的字符的欺骗攻击。
字符串"paypal.com"是一个臭名昭著的例子，字母可能是拉丁文和西里尔文的混合体。
此模式确保在空格后的非空格序列中匹配的字符是拉丁语系：

\s+(?=\p{Latin})(*sr:\S+)

|  运行脚本类| 描述|
|---                                    |---                                                                |
|(*script_run:...)                      |脚本运行，可被回溯到                                               |
|(*sr:...)                              |脚本运行，可被回溯到                                               |
|(*atomic_script_run:...)               |原子脚本运行                                                       |
|(*asr:...)                             |原子脚本运行                                                       |

## 引用：

YashanDB的正则表达仅支持在模式内部进行反向引用，不支持在regexp_replace函数的replace参数处使用。

### 表10-1：反向引用符

|  反向引用符| 描述|
|---                                    |---                                                                |
|\n                                     |编号引用（可能不明确）                                             |
|\gn                                    |编号引用                                                           |
|\g{n}                                  |编号引用                                                           |
|\g+n                                   |相对编号引用（扩展）                                                 |
|\g-n                                   |相对编号引用                                                       |
|\g{+n}                                 |相对编号引用（扩展）                                                 |
|\g{-n}                                 |相对编号引用                                                       |
|\k\<name>                               |编号引用（Perl）                                                     |
|\k'name'                               |编号引用（Perl）                                                     |
|\g{name}                               |编号引用（Perl）                                                     |
|\k{name}                               |编号引用（.NET）                                                     |
|(?P=name)                              |编号引用（Python）                                                   |

### 表10-2：子例程引用符（可能递归）

|  子例程引用符| 描述|
|---                                    |---                                                                |
|(?R)                                   |递归整个模式                                                       |
|(?n)                                   |按绝对数调用子例程                                                 |
|(?+n)                                  |按相对数调用子例程                                                 |
|(?-n)                                  |按相对数调用子例程                                                 |
|(?&name)                               |按名称调用子例程（Perl）                                             |
|(?P>name)                              |按名称调用子例程（Python）                                           |
|\g\<name>                               |按名称调用子例程（Oniguruma）                                        |
|\g'name'                               |按名称调用子例程（Oniguruma）                                        |
|\g\<n>                                  |按绝对数调用子例程（Oniguruma）                                      |
|\g'n'                                  |按绝对数调用子例程（Oniguruma）                                      |
|\g\<+n>                                 |按相对编号调用子例程（扩展）                                         |
|\g'+n'                                 |按相对编号调用子例程（扩展）                                         |
|\g\<-n>                                 |按相对编号调用子例程（扩展）                                         |
|\g'-n'                                 |按相对编号调用子例程（扩展）                                         |

## 条件模式：

### 表11-1：条件模式调用

|  条件模式| 描述|
|---                                    |---                                                                |
|(?(condition)yes-pattern)              |condition符合即使用yes模式匹配，失败不匹配                         |
|(?(condition)yes-pattern&#124;no-pattern)   |condition符合即使用yes模式匹配，失败使用no模式匹配                 |

### 表11-2：条件类

|  条件类| 描述|
|---                                    |---                                                                |
|(?(n)                                  |绝对参考条件                                                       |
|(?(+n)                                 |相对参考条件                                                       |
|(?(-n)                                 |相对参考条件                                                       |
|(?(\<name>)                             |命名参考条件（Perl）                                                 |
|(?('name')                             |命名参考条件（Perl）                                                |
|(?(name)                               |命名参考条件（已弃用）                                              |
|(?(R)                                  |总体递归条件                                                       |
|(?(Rn)                                 |特定编号组递归条件                                                 |
|(?(R&name)                             |特定的命名组递归条件                                               |
|(?(DEFINE)                             |定义组以供参考                                                     |
|(?(VERSION[>]=n.m)                     |测试版本                                                           |
|(?(assert)                             |断言条件                                                           |

## 回溯控制：

### 表12-1：回溯控制符

所有回溯控制动词的格式可以是(\*VERB:NAME)，对于(\*MARK)，名称是必填的，对于其他名称是可选的。

注：
1. 第四项-第八项表示仅当后续匹配失败导致回溯到达它们时，才执行对应操作。
它们都会迫使匹配失败，但它们在匹配后会发生什么情况上有所不同。
只有在模式未锚定的情况下，才会提前匹配点的起点。

2. 在称为子例程的组中，这些动词之一的作用仅限于子例程调用。

|  回溯控制符| 描述|
| ------------ | ----------------------------------------------------------- |
| (*ACCEPT)    | 强制成功匹配                                                |
| (*FAIL)      | 强制回溯；同义词(\*F)                                       |
| (*MARK:NAME) | 设置要传回的名称；同义词(\*:NAME)                           |
| (*COMMIT)    | 总体失败，起点不提前                                        |
| (*PRUNE)     | 前进到下一个起始字符                                        |
| (*SKIP)      | 前进到较早的(\*MARK:NAME)的位置，如果没找到，则忽略(\*SKIP) |
| (*SKIP:NAME) | 绝对参考条件                                                |
| (*THEN)      | 局部失败，返回到下一个替换项                                |

## 标注：

### 表13-1：标注符

允许的字符串分隔符为“'”^%\$（起始和结束的分隔符相同），起始分隔符{与结束分隔符匹配}。
要在字符串中对结束分隔符进行匹配，请重复输入它。

|  标注| 描述|
|---                                    |---                                                                |
|(?C)                                   |标注（假定编号0）                                                    |
|(?Cn)                                  |标注数字数据n                                                      |
|(?C"text")                             |带字符串数据的标注                                                 |
