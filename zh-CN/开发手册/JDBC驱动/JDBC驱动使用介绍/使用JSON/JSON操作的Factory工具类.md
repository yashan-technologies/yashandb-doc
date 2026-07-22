YashanDB JDBC驱动提供了YasonFactory工具类来获取定义的json对象模型和流读取器，写入器。
主要方法如下：

|  方法| 含义|
|-----------------------------------------------------------|----------------------------------------------|
| YasonFactory()                                            | 无参构造方法                                       |
| YasonGenerator createJsonTextGenerator(OutputStream var1) | 创建一个往给定OutputStream对象里写入生成的json字符串的YasonGenerator |
| YasonGenerator createJsonTextGenerator(Writer var1)       | 创建一个往给定Writer对象里写入生成的json字符串的YasonGenerator |
| YasonParser createJsonTextParser(java.io.Reader)          | 创建一个读取Reader对象里面的json字符串来进行解析的YasonParser    |
| YasonParser createJsonTextParser(java.io.InputStream)     | 创建一个读取InputStream对象里面的json字符串来进行解析的YasonParser |       
| YasonValue createJsonTextValue(java.io.Reader)            | 根据Reader对象里面的json字符串来解析出一个YasonValue         |
| YasonValue createJsonTextValue(java.io.InputStream)       | 根据InputStream对象里面的json字符串来解析出一个YasonValue    |       
| YasonObject createObject()                                | 创建一个空的YasonObject对象                          |       
| YasonObject createObject(YasonObject var1)                | 根据yasonObject对象创建一个新的YasonObject对象           |       
| YasonArray createArray()                                  | 创建一个空的YasonArray对象                           |       
| YasonArray createArray(YasonArray var1)                   | 根据YasonArray对象创建一个新的YasonArray对象             |       
| YasonString createString(String var1)                     | 创建一个值为指定String的YasonString                   |       
| YasonDecimal createDecimal(String var1)                   | 创建一个值为指定Decimal的YasonDecimal                 |       
| YasonInt createInt(int var1)                              | 创建一个值为指定int的YasonInt                         |       
| YasonShort createShort(short var1)                        | 创建一个值为指定short的YasonShort                     |       
| YasonByte createByte(byte var1)                           | 创建一个值为指定byte的YasonByte                       |       
| YasonLong createLong(long var1)                           | 创建一个值为指定long的YasonLong                       |       
| YasonFloat createFloat(float var1)                        | 创建一个值为指定float的YasonFloat                     |       
| YasonDouble createDouble(double var1)                     | 创建一个值为指定double的YasonDouble                   |       
| YasonBoolean createBoolean(boolean var1)                  | 创建一个值为指定boolean的YasonBoolean                 |       
| YasonBinary createBinary(byte[] var1)                     | 创建一个值为指定byte[]的YasonBinary                   |       
| YasonTimestamp createTimestamp(Timestamp var1)            | 创建一个值为指定Timestamp的YasonTimestamp             |       
| YasonTimestamp createTimestamp(LocalDateTime var1)        | 创建一个值为指定LocalDateTime的YasonTimestamp         |       
| YasonDate createDate(Date var1)                           | 创建一个值为指定Date的YasonDate                       |       
| YasonDate createDate(LocalDateTime var1)                  | 创建一个值为指定LocalDateTime的YasonDate              |       
| YasonDate createDate(LocalDate var1)                      | 创建一个值为指定LocalDate的YasonDate                  |       
| YasonTime createTime(Time var1)                           | 创建一个值为指定Time的YasonTime                       |       
| YasonTime createTime(LocalTime var1)                      | 创建一个值为指定LocalTime的YasonTime                  |       

### YasonFactory使用示例

```java
        YasonFactory factory = new YasonFactory();

        YasonInt yasonInt = factory.createInt(2); // 创建一个值为2的YasonInt

        YasonObject yasonObject = factory.createObject(); // 创建一个空的YasonObject
        yasonObject.put("key1",yasonInt);
        System.out.println(yasonObject); // {"key1":2}

        YasonArray yasonArray = factory.createArray(); // 创建一个空的YasonArray
        yasonArray.add(yasonObject);
        yasonArray.add("value2");
        yasonArray.add(yasonInt);
        System.out.println(yasonArray); // [{"key1":2},"value2",2]
        System.out.println(yasonArray.getString(1)); // value2
        
```
