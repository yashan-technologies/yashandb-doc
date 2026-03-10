PL视图，显示过程体参数信息。

|  字段| 类型| 说明|
|----------------------|--------------|------------------------------------------------------------------|
| OWNER                | VARCHAR(64)  | 参数所属用户名                                                          |
| OBJECT_NAME         | VARCHAR(64)  | 参数所属过程体或函数名称或包名                                                  |
| SUBPROGRAM_NAME     | VARCHAR(68)  | 参数所属包名中所属子过程体或函数名称<sup>*</sup>                                   |
| PACKAGE_NAME        | VARCHAR(64)  | 参数所属包名（对于UDT OBJECT方法的参数，此值为UDT名称）                               |
| OBJECT_ID           | BIGINT       | 参数的对象ID                                                          |
| OVERLOAD             | VARCHAR(1)   | 同名重载时该参数的重载标识<sup>*</sup>                                        |
| SUBPROGRAM_ID       | INTEGER      | 参数所属函数在所属高级包中的ID<sup>*</sup>                                     |
| ARGUMENT_NAME       | VARCHAR(68)  | 参数名称                                                             |
| POSITION             | INTEGER      | 该参数在参数列表中的位置，返回值为0，非返回值参数从1开始                                    |
| SEQUENCE             | BIGINT       | 该参数在参数列表中的序列，从1开始（包括返回值）                                         |
| DATA_LEVEL          | INTEGER      | 复合类型参数的嵌套深度<sup>(1)</sup>                                        |
| DATA_TYPE           | VARCHAR(64)  | 参数的数据类型                                                          |
| DEFAULTED            | VARCHAR(1)   | 该参数是否有默认值                                                        |
| DEFAULT_VALUE       | CLOB         | 该参数的默认值                                                          |
| DEFAULT_LENGTH      | BIGINT       | 参数默认值的长度（字符长度）                                                   |
| IN_OUT              | VARCHAR(6)   | 参数的出入方向                                                          |
| DATA_LENGTH         | INTEGER      | 参数长度（字符长度）                                                       |
| DATA_PRECISION      | INTEGER      | NUMBER类型数据精度                                                     |
| DATA_SCALE          | INTEGER      | NUMBER类型数据尺度                                                     |
| RADIX                | VARCHAR(11)  | 基数，TINYINT/SMALLINT/INTEGER/BIGINT/NUMBER类型返回10，BIT类型返回2，其他类型返回空 |
| CHARACTER_SET_NAME | VARCHAR(9)   | 参数所使用的字符集                                                        |
| TYPE_OWNER          | VARCHAR(68)  | 参数类型的持有用户<sup>*</sup>                                            |
| TYPE_NAME           | VARCHAR(68)  | 参数类型的名称。如果类型是包本地类型（即在包规范中声明），则此列显示包的名称<sup>*</sup>               |
| TYPE_SUBNAME        | VARCHAR(1)   | 当包为本地类型时，在此列显示类型名称<sup>*</sup>                                   |
| TYPE_LINK           | VARCHAR(1)   | 当TYPE_NAME列中标识的包是远程包时，仅与包本地类型相关，此列显示用于引用远程包的数据库链接<sup>*</sup>   |
| TYPE_OBJECT_TYPE   | VARCHAR(1)   | 显示通过TYPE_NAME，TYPE_SUBNAME描述的类型的名称<sup>*</sup>                 |
| PLS_TYPE            | VARCHAR(64)  | 参数的PL类型<sup>(2)</sup>                                            |
| CHAR_LENGTH         | VARCHAR(1)   | 当为字符串相关类型时，显示该类型的长度限制<sup>*</sup>                                |
| CHAR_USED           | VARCHAR(1)   | 表明该字符串是字节显示还是字符限制<sup>*</sup>                                    |
| ORIGIN_CON_ID      | VARCHAR(1)   | 容器原始行号<sup>*</sup><sup>(3)</sup>                                 |

带\*号的列恒为空或N，用作功能预留。

(1) 该列恒为0。

(2) YashanDB所支持的PL类型与本身所持有类型一致。

(3) 该列同DBA_PROCEDURES。
