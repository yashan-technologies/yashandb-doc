com.yashandb.json包里面定义了YashanDB目前支持的json的16种类型对象模型的含义以及对应关系：

| YasonType       | code | JSON类型对象模型  | 含义                                           |
|-----------------|------|-------------|----------------------------------------------|
| YASON_OBJECT    | 1    | YasonObject | json标准类型，代表json的Object类型。                     |
| YASON_ARRAY     | 2    | YasonArray  | json标准类型，代表json的Array类型。                      |
| YASON_STRING    | 3    | YasonString | json标准类型，代表json的字符串类型。                       |
| YASON_NUMBER    | 4    | YasonDecimal | json标准类型，代表json的数字类型。                        |
| YASON_BOOLEAN   | 5    | YasonBoolean | json标准类型，代表json的布尔类型。                        |
| YASON_NULL      | 6    | YasonNull   | json标准类型，代表json的null类型。                      |
| YASON_BYTE      | 7    | YasonByte   | json扩展类型，是对json的数字类型的细分，对应-128~127范围内的整数类型。 |
| YASON_SHORT     | 8    | YasonShort  | json扩展类型，是对json的数字类型的细分，对应java的short类型。      |
| YASON_INT       | 9    | YasonInt    | json扩展类型，是对json的数字类型的细分，对应java的int类型。        |
| YASON_BIGINT    | 10   | YasonLong   | json扩展类型，是对json的数字类型的细分，对应java的long类型。       |
| YASON_FLOAT     | 11   | YasonFloat  | json扩展类型，是对json的数字类型的细分，对应java的float类型。      |
| YASON_DOUBLE    | 12   | YasonDouble | json扩展类型，是对json的数字类型的细分，对应java的double类型。     |
| YASON_BINARY    | 13   | YasonBinary | json扩展类型，表示二进制类型，对应java里面的byte数组。            |
| YASON_TIMESTAMP | 14   | YasonTimestamp | json扩展类型，对应java里面的Timestamp类型。               |
| YASON_DATE      | 15   | YasonDate   | json扩展类型，对应java里面的Date类型。                    |
| YASON_TIME      | 16   | YasonTime   | json扩展类型，对应java里面的Time类型。                    |

注意：其中3~16的这些类型都为非标量类型，创建后它所包含的值就不可修改。

上面16种对象模型都继承自YasonValue父级接口，YasonValue包含如下方法：

| 返回类型      | 方法                                  | 备注                     |
|-----------|-------------------------------------|------------------------|
| YasonType | getType()                           | 获取json类型。              |
| int       | getDepth() throws SQLException      | 获取json深度，深度超过100会抛出异常。 |
| Object    | getValue()                          | 获取json内部实际的值。          | 
| String    | getString()                         | 获取json的值的字符串形式。        |
| byte[]    | getBinaryData() throws SQLException | 获取json编码成的二进制数据。      |
| String    | toString()                          | 获取json格式的字符串。          |
