YashanDB JDBC驱动里面定义了YasonObject对象模型，用来表示key-value形式的JSONObject类型的数据，其中key永远为字符串，value为YasonValue类型。

它包含两个构造方法：

| 方法                                   | 备注                                                  |
|--------------------------------------|-----------------------------------------------------|
| YasonObject()                        | 创建一个空的json对象                                        |
| YasonObject(YasonObject yasonObject) | 创建一个空的json对象，并且把入参的yasonObject的key-value对全部加到这个对象里  |

它包含如下普通方法：

| 返回类型                                | 方法                                                       | 备注                                                  |
|-------------------------------------|----------------------------------------------------------|-----------------------------------------------------|
| YasonValue                          | get(Object key)                                          | 根据key获取value                                        |
| YasonValue                          | get(Object key,YasonValue defaultValue)                  | 根据key获取value，为空则返回默认值                               |
| YasonValue                          | put(String key, YasonValue value)                        | 添加键值对                                               |
| YasonValue                          | putNull(String key)                                      | 添加值为YasonNull的键值对                                   |
| boolean                             | isNull(String key)                                       | 获取某一个key的value是否为YasonNull                          |
| YasonValue                          | put(String key, String value)                            | 添加键值对，入参String类型的value会自动转化成YasonString存入           |
| String                              | getString(String key)                                    | 根据key获取字符串类型的值，会尝试进行类型转换                            |
| String                              | getString(String key,String defaultValues)               | 根据key获取字符串类型的值，会尝试进行类型转换，key不存在或类型转换失败则返回默认值        |
| YasonValue                          | put(String key, BigDecimal value)                        | 添加键值对，入参BigDecimal类型的value会自动转化成YasonDecimal存入      |
| BigDecimal                          | getBigDecimal(String key)                                | 根据key获取BigDecimal类型的值，会尝试进行类型转换                     |
| BigDecimal                          | getBigDecimal(String key,BigDecimal defaultValues)       | 根据key获取BigDecimal类型的值，key不存在或类型转换失败则返回默认值           |
| YasonValue                          | put(String key, boolean value)                           | 添加键值对，入参boolean类型的value会自动转化成YasonBoolean存入         |
| boolean                             | getBoolean(String key)                                   | 根据key获取boolean类型的值                                  |
| boolean                             | getBoolean(String key,boolean defaultValues)             | 根据key获取boolean类型的值，key不存在或类型不符则返回默认值                |
| YasonValue                          | put(String key, byte value)                              | 添加键值对，入参byte类型的value会自动转化成YasonByte存入               |
| byte                                | getByte(String key)                                      | 根据key获取byte类型的值，会尝试进行类型转换                           |
| byte                                | getByte(String key,byte defaultValues)                   | 根据key获取byte类型的值，key不存在或类型转换失败则返回默认值                 |
| YasonValue                          | put(String key, short value)                             | 添加键值对，入参short类型的value会自动转化成YasonShort存入             |
| short                               | getShort(String key)                                     | 根据key获取short类型的值，会尝试进行类型转换                          |
| short                               | getShort(String key,short defaultValues)                 | 根据key获取short类型的值，key不存在或类型转换失败则返回默认值                |
| YasonValue                          | put(String key, int value)                               | 添加键值对，入参short类型的value会自动转化成YasonInt存入               |
| int                                 | getInt(String key)                                       | 根据key获取int类型的值，会尝试进行类型转换                            |
| int                                 | getInt(String key,int defaultValues)                     | 根据key获取int类型的值，key不存在或类型转换失败则返回默认值                  |
| YasonValue                          | put(String key, long value)                              | 添加键值对，入参long类型的value会自动转化成YasonLong存入               |
| long                                | getLong(String key)                                      | 根据key获取long类型的值，会尝试进行类型转换                           |
| long                                | getLong(String key,long defaultValues)                   | 根据key获取long类型的值，key不存在或类型转换失败则返回默认值                 |
| YasonValue                          | put(String key, float value)                             | 添加键值对，入参float类型的value会自动转化成YasonFloat存入             |
| float                               | getFloat(String key)                                     | 根据key获取float类型的值，会尝试进行类型转换                          |
| float                               | getFloat(String key,float defaultValues)                 | 根据key获取float类型的值，key不存在或类型转换失败则返回默认值                |
| YasonValue                          | put(String key, double value)                            | 添加键值对，入参double类型的value会自动转化成YasonDouble存入           |
| double                              | getDouble(String key)                                    | 根据key获取double类型的值，会尝试进行类型转换                         |
| double                              | getDouble(String key,double defaultValues)               | 根据key获取double类型的值，key不存在或类型转换失败则返回默认值               |
| YasonValue                          | put(String key, byte[] value)                            | 添加键值对，入参byte[]类型的value会自动转化成YasonBinary存入           |
| byte[]                              | getBytes(String key)                                     | 根据key获取byte[]类型的值                                   |
| byte[]                              | getBytes(String key,byte[] defaultValues)                | 根据key获取byte[]类型的值，key不存在或类型不符则返回默认值                 |
| YasonValue                          | put(String key, Date value)                              | 添加键值对，入参Date类型的value会自动转化成YasonDate存入               |
| Date                                | getDate(String key)                                      | 根据key获取Date类型的值，会尝试进行类型转换                           |
| Date                                | getDate(String key,Date defaultValues)                   | 根据key获取Date类型的值，key不存在或类型转换失败则返回默认值                 |
| YasonValue                          | put(String key, LocalDate value)                         | 添加键值对，入参LocalDate类型的value会自动转化成YasonDate存入          |
| LocalDate                           | getLocalDate(String key)                                 | 根据key获取LocalDate类型的值，会尝试进行类型转换                      |
| LocalDate                           | getLocalDate(String key,LocalDate defaultValues)         | 根据key获取LocalDate类型的值，key不存在或类型转换失败则返回默认值            |
| YasonValue                          | put(String key, LocalTime value)                         | 添加键值对，入参LocalTime类型的value会自动转化成YasonTime存入          |
| LocalTime                           | getLocalTime(String key)                                 | 根据key获取LocalTime类型的值，会尝试进行类型转换                      |
| LocalTime                           | getLocalTime(String key,LocalTime defaultValues)         | 根据key获取LocalTime类型的值，key不存在或类型转换失败则返回默认值            |
| YasonValue                          | put(String key, Time value)                              | 添加键值对，入参Time类型的value会自动转化成YasonTime存入               |
| Time                                | getTime(String key)                                      | 根据key获取Time类型的值，会尝试进行类型转换                           |
| Time                                | getTime(String key,Time defaultValues)                   | 根据key获取Time类型的值，key不存在或类型转换失败则返回默认值                 |
| YasonValue                          | put(String key, Timestamp value)                         | 添加键值对，入参Timestamp类型的value会自动转化成YasonTimestamp存入     |
| Timestamp                           | getTimestamp(String key)                                 | 根据key获取Timestamp类型的值，会尝试进行类型转换                      |
| Timestamp                           | getTimestamp(String key,Timestamp defaultValues)         | 根据key获取Timestamp类型的值，key不存在或类型转换失败则返回默认值            |
| YasonValue                          | put(String key, LocalDateTime value)                     | 添加键值对，入参LocalDateTime类型的value会自动转化成YasonTimestamp存入 |
| LocalDateTime                       | getLocalDateTime(String key)                             | 根据key获取LocalDateTime类型的值，会尝试进行类型转换                  |
| LocalDateTime                       | getLocalDateTime(String key,LocalDateTime defaultValues) | 根据key获取LocalDateTime类型的值，key不存在或类型转换失败则返回默认值        |
| int                                 | size()                                                   | 获取jsonObject的键值对个数                                  |
| boolean                             | isEmpty()                                                | 是否为空                                                |
| boolean                             | containsKey(Object key)                                  | 是否包含某key                                            | 
| boolean                             | containsValue(Object value)                              | 是否包含某value                                          |
| YasonValue                          | remove(Object key)                                       | 从json中移除key                                         |
| void                                | putAll(Map\<? extends String, ? extends YasonValue> m)    | 把入参map里面的键值对添加到json里                                |
| void                                | clear()                                                  | 清空json                                              |
| Collection\<YasonValue>              | values()                                                 | 获取json里面的全部value                                    |
| Set<Map.Entry\<String, YasonValue>>  | entrySet()                                               | 获取json里面的全部键值对                                      |

YasonObject使用示例如下：

```java
        YasonObject json = new YasonObject();
        json.put("key1","value1");
        json.put("key2",34.3);
        json.put("key3",new YasonInt(2));
        json.put("key4",new byte[]{97,97,97});
        json.put("key5",LocalDate.of(2023,8,30));

        System.out.println(json.size()); // 5
        System.out.println(json.containsKey("key2")); // true

        System.out.println(json.getString("key1")); // value1
        System.out.println(json.getInt("key2")); // 34
        System.out.println(Arrays.toString(json.getBytes("key4"))); // [97,97,97]
        System.out.println(json.getString("key5")); // 2023-08-30T00:00:00

        System.out.println(json.getInt("key1", 2)); // 2, key1的值是”value1“，不是int类型，所以getInt会返回默认值
        System.out.println(json.getString("key6", "defaultStrValue")); // defaultStrValue, json中不存在key6这个键，所以返回默认值

        YasonObject json1 = new YasonObject();
        json1.put("key6", json); // 可以把一个YasonObject作为value放到另一个YasonObject中
        System.out.println(((YasonObject)json1.get("key6")).getString("key1")); // {"key6":{"key1":"value1","key2":34.3,"key3":2,"key4":"616161","key5":"2023-08-30T00:00:00"}}
        
        json.clear();
        System.out.println(json.size()); // 0
```