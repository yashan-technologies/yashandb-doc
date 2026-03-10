com.yashandb.json包中的类和接口用于SQL JSON类型的处理，使用此程序包可以：

- 在数据库中存储和检索JSON类型值。
- 读取、创建和修改JSON类型值。
- 以数据库使用的相同二进制JSON存储格式对JSON类型值进行编码/解码。
- 将JSON类型值转换为JSON文本和从JSON文本转换JSON类型值。

com.yashandb.json包含以下三部分：

| 描述                          | 接口         |
|--------------------------------|--------------------------------|
| JSON类型对象模型                  | YasonValue,YasonObject,YasonArray,YasonString,YasonDecimal,<br/>YasonBoolean,YasonNull,YasonByte,YasonShort,YasonInt,<br/>YasonLong,YasonFloat,YasonDouble,YasonBinary<br/>YasonDate,YasonTime,YasonTimestamp |
| JSON类型的事件流读取器和写入器       | YasonGenerator, YasonParser               |
| 读取、写入和创建JSON类型值的工厂     |  YasonFactory                 |

此模块接口不适用于分布式部署。