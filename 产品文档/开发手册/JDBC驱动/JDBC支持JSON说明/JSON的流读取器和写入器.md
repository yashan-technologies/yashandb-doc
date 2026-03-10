YashanDB JDBC驱动具备对流对象进行读取解析成json的能力（流读取器）和往流对象里面写入json串的能力（流写入器）。

### 流读取器YasonParser
解析流对象（Reader或InputStream）里面包含的json字符串，通过逐步读取的方式解析出完整的json对象或者获取json中某个数据。

YasonParser对象一般通过YasonFactory类里面的如下两个方法获取：

| 方法                                       | 备注                             |
|------------------------------------------|--------------------------------|
| createJsonTextParser(java.io.Reader)     | 读取Reader对象里面的json字符串来进行解析      |
| createJsonTextValue(java.io.InputStream) | 读取InputStream对象里面的json字符串来进行解析 |

YasonParser的操作接口如下：

| 方法                          | 备注                                                        |
|-----------------------------|-----------------------------------------------------------|
| boolean hasNext()           | 是否还有下一个元素                                                 |
| Event next()                | 读取下一个元素，返回值Event代表读取到的下一个元素的类型，见下面YasonParser.Event类型介绍小节 |
| String getString()          | 获取当前读到的字符串，如果读到的不是key或字符串或数字则报错                           |
| boolean isIntegralNumber()  | 判断当前解析到的数字是否为整数，如果不是数字类型则报错                               |
| int getInt()                | 获取当前读到的数字的int值，如果读到的不是数字则报错                               |
| long getLong()              | 获取当前读到的数字的long值，如果读到的不是数字则报错                              |
| double getDouble()          | 获取当前读到的数字的double值，如果读到的不是数字则报错                            |
| float getFloat()            | 获取当前读到的数字的float值，如果读到的不是数字则报错                             |
| BigInteger getBigInteger()  | 获取当前读到的数字的BigInteger值，如果读到的不是数字则报错                        |
| BigDecimal getBigDecimal()  | 获取当前读到的数字的BigDecimal值，如果读到的不是数字则报错                        |
| YasonValue getValue()       | 获取当前读到的YasonValue值                                        |
| YasonArray getArray()       | 获取当前读到的YasonArray值，如果类型不符则报错                              |
| YasonObject getObject()     | 获取当前读到的YasonObject值，如果类型不符则报错                             |
| void skipArray()            | 跳过下一个YasonArray值，类型不是YasonArray则忽略                        |
| void skipObject()           | 跳过下一个YasonObject值，类型不是YasonObject则忽略                      |
| void close()                | 关闭入参中的流，停止解析json串                                         |

#### YasonParser.Event类型介绍
YasonParser.Event是一个枚举类型，用来描述YasonParser从流中解析到的当前元素的类型，有以下取值：

| 枚举值           | 含义                             |
|---------------|--------------------------------|
| START_ARRAY   | 解析到了Array的开始符号，即json串中的左方括号`[` |
| START_OBJECT  | 解析到了OBJECT的开始，即json串中的左花括号`{`  |
| KEY_NAME      | 解析到了OBJECT的key                 |
| VALUE_STRING  | 解析到了String类型的value             |
| VALUE_DECIMAL | 解析到了数字类型的value                 |
| VALUE_TRUE    | 解析到了布尔值true                    |
| VALUE_FALSE   | 解析到了布尔值false                   |
| VALUE_NULL    | 解析到了json中的null值                |
| END_OBJECT    | 解析到了OBJECT的结束，即json串中的右花括号`}`  |
| END_ARRAY     | 解析到了Array的结束，即json串中的右方括号`]`   |

#### YasonParser使用示例

示例一：该示例简单演示了何如从一个json字符串包含的流对象中解析出json对象的每一个值：
```java
        YasonFactory factory = new YasonFactory();
        String json = "{\"key1\":\"value1\",\"key2\":false,\"key3\":[\"value2\",33,true,null],\"key4\":false,\"\":23}";
        
        YasonParser parser = factory.createJsonTextParser(new StringReader(json));
        while (parser.hasNext()) {
            YasonParser.Event event = parser.next();
            System.out.println(event);
            switch (event) {
                case KEY_NAME:
                case VALUE_STRING:
                case VALUE_DECIMAL:
                    System.out.println(parser.getString());
                    break;
                case VALUE_FALSE:
                case VALUE_TRUE:
                case VALUE_NULL:
                    System.out.println(parser.getValue());
                default:
            }
        }
       
```

示例二：该示例演示了何如从一个json字符串包含的流对象中解析出一个完整的json对象：
```java
        YasonFactory factory = new YasonFactory();
        String json = "{\"key1\":\"value1\",\"key2\":false,\"key3\":[\"value2\",33,true,null],\"key4\":false,\"key5\":23}";
        YasonParser parser = factory.createJsonTextParser(new StringReader(json));
        System.out.println(parser.hasNext()); // true
        System.out.println(parser.next()); // START_OBJECT,解析json得到类型为START_OBJECT
        YasonObject object = parser.getObject(); // 直接通过getObject接口获取YasonObject对象
        
        //可以对解析出来的YasonObject进行各种操作
        System.out.println(object); // {"key1":"value1","key2":false,"key3":["value2",33,true,null],"key4":false,"key5":23}
        System.out.println(object.get("key1")); // "value1"
        System.out.println(object.get("key3").getType()); // YASON_ARRAY
        System.out.println(object.get("key3")); // ["value2",33,true,null]
       
```

### 流写入器YasonGenerator
通过传入key，value或者对象的方式生成一个json串写入到流对象里面。

YasonGenerator对象一般通过YasonFactory类里面的如下两个方法获取：

| 方法                                       | 备注                               |
|------------------------------------------|----------------------------------|
| createJsonTextGenerator(Writer var1)     | 创建一个往给定Writer对象里写入生成的json字符串的YasonGenerator    |
| createJsonTextGenerator(OutputStream var1) | 创建一个往给定OutputStream对象里写入生成的json字符串的YasonGenerator |

YasonGenerator的操作接口如下：

| 方法                                                   | 含义                                                                                  |
|------------------------------------------------------|-------------------------------------------------------------------------------------|
| YasonGenerator writeStartObject()                    | 往流对象里面写一个Object的开始符，即左花括号`{`                                                       |
| YasonGenerator writeStartArray()                     | 往流对象里面写一个Array的开始符，即左方括号`[`                                                       |
| YasonGenerator writeKey(String value)                | 往流对象里面写一个object的key值                                                                |
| YasonGenerator write(YasonValue value)               | 往流对象里面写一个YasonValue类型的value值                                                        |
| YasonGenerator write(String value)                   | 往流对象里面写一个String类型的value值                                                            |
| YasonGenerator write(Number value)                   | 往流对象里面写一个Number类型的value值（常见的int，double，BigDecimal等类型的值都可以通过该接口写入）                   |
| YasonGenerator write(boolean value)                  | 往流对象里面写一个boolean类型的value值                                                           |
| YasonGenerator write(LocalDateTime value)            | 往流对象里面写一个LocalDateTime类型的value值                                                     |
| YasonGenerator write(byte[] value)                   | 往流对象里面写一个byte[]类型的value值                                                            |
| YasonGenerator writeNull()                           | 往流对象里面写一个null值                                                                      |
| YasonGenerator writeStartObject(String key)          | 先写一个key，再写一个Object的开始符                                                              |
| YasonGenerator writeStartArray(String key)           | 先写一个key，再写一个Array的开始符                                                               |
| YasonGenerator write(String key,YasonValue value)    | 先写一个key，再写YasonValue类型的value值                                                       |
| YasonGenerator write(String key,String value)        | 先写一个key，再写String类型的value值                                                           |
| YasonGenerator write(String key,Number value)        | 先写一个key，再写Number类型的value值                                                           |
| YasonGenerator write(String key,boolean value)       | 先写一个key，再写boolean类型的value值                                                          |
| YasonGenerator write(String key,LocalDateTime value) | 先写一个key，再写LocalDateTime类型的value值                                                    |
| YasonGenerator write(String key,byte[] value)        | 先写一个key，再写byte[]类型的value值                                                           |
| YasonGenerator writeNull(String key)                 | 先写一个key，再写一个null值                                                                   |
| YasonGenerator writeEnd()                            | 写一个结束符，如果当前是未闭合的Object，就会写Object的结束符（右花括号`}`）， 如果当前是未闭合的Array，就会写Array的结束符（右方括号`]`） |
| void close()                                         | 关闭流对象                                                                               |
| void flush()                                         | 刷新流对象                                                                               |

>**Note**:
>
> YasonGenerator的所有write方法都会返回YasonGenerator自身对象，因此在实际应用中可以链式调用。
>
> 所有write方法都应该被正确的调用，否则会抛出异常。

#### YasonGenerator使用示例

示例一：该示例简单演示了如何依次写入多个值，最终生成一个json串到输出流中：
```java
        YasonFactory factory = new YasonFactory();
        Writer writer = new StringWriter();
        YasonGenerator generator = factory.createJsonTextGenerator(writer);
        generator.writeStartObject();
        generator.writeKey("key1");
        generator.write("value");

        generator.writeKey("key2").write(2).writeKey("key3").write("value3"); // 链式调用

        generator.writeKey("key4");
         // 在一个json object中，key后面必须跟着一个value，所以必须调write去写一个value,如果此时再调writeKey方法或者writeEnd方法就会抛出异常。
        generator.write(new BigDecimal(32e2));

        YasonArray array = new YasonArray();
        array.add("arrayValue1");
        array.add(3);
        array.add(new byte[]{97,97,97});
        array.add(LocalDateTime.of(2023,8,23,15,34,56));
        generator.write("key5",array);
        generator.writeEnd();
        generator.close();
        // 写入完成，后面就可以通过writer.toString()取出生成出来的json串了
        System.out.println(writer.toString()); //{"key1":"value","key2":2,"key3":"value3","key4":3200,"key5":["arrayValue1",3,"616161","2023-08-23T15:34:56"]}
       
```





