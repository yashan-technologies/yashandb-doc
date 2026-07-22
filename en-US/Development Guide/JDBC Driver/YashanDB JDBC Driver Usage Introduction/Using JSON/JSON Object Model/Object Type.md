The YasonDB JDBC driver defines the YasonObject object model to represent key-value pairs in the JSON format, where the key is always a string and the value is of type YasonValue.

It contains two constructors:

|Method |Description |
|--------------------------------------|-----------------------------------------------------|
| YasonObject()                       | Creates an empty JSON object                          |
| YasonObject(YasonObject yasonObject)| Creates an empty JSON object and adds all key-value pairs from the provided yasonObject to this object |

It contains the following common methods:

|Return Type |Method |Description |
|-------------------------------------|----------------------------------------------------------|-----------------------------------------------------|
| YasonValue                          | get(Object key)                                     | Retrieves the value based on the key                  |
| YasonValue                          | get(Object key, YasonValue defaultValue)           | Retrieves the value based on the key, returns default value if empty    |
| YasonValue                          | put(String key, YasonValue value)                  | Adds a key-value pair                                  |
| YasonValue                          | putNull(String key)                                 | Adds a key-value pair with value as YasonNull        |
| boolean                             | isNull(String key)                                  | Checks if the value for a specific key is YasonNull |
| YasonValue                          | put(String key, String value)                       | Adds a key-value pair, converts String type value to YasonString     |
| String                              | getString(String key)                               | Retrieves the string value based on the key, attempts type conversion  |
| String                              | getString(String key, String defaultValues)        | Retrieves the string value based on the key, attempts type conversion, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, BigDecimal value)                  | Adds a key-value pair, converts BigDecimal type value to YasonDecimal  |
| BigDecimal                          | getBigDecimal(String key)                           | Retrieves the BigDecimal value based on the key, attempts type conversion |
| BigDecimal                          | getBigDecimal(String key, BigDecimal defaultValues) | Retrieves the BigDecimal value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, boolean value)                      | Adds a key-value pair, converts boolean type value to YasonBoolean    |
| boolean                             | getBoolean(String key)                              | Retrieves the boolean value based on the key         |
| boolean                             | getBoolean(String key, boolean defaultValues)      | Retrieves the boolean value based on the key, returns default value if key doesn't exist or type mismatch occurs |
| YasonValue                          | put(String key, byte value)                         | Adds a key-value pair, converts byte type value to YasonByte                |
| byte                                | getByte(String key)                                 | Retrieves the byte value based on the key, attempts type conversion        |
| byte                                | getByte(String key, byte defaultValues)             | Retrieves the byte value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, short value)                        | Adds a key-value pair, converts short type value to YasonShort              |
| short                               | getShort(String key)                                | Retrieves the short value based on the key, attempts type conversion         |
| short                               | getShort(String key, short defaultValues)          | Retrieves the short value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, int value)                          | Adds a key-value pair, converts int type value to YasonInt                  |
| int                                 | getInt(String key)                                  | Retrieves the int value based on the key, attempts type conversion          |
| int                                 | getInt(String key, int defaultValues)              | Retrieves the int value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, long value)                         | Adds a key-value pair, converts long type value to YasonLong                |
| long                                | getLong(String key)                                 | Retrieves the long value based on the key, attempts type conversion          |
| long                                | getLong(String key, long defaultValues)            | Retrieves the long value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, float value)                        | Adds a key-value pair, converts float type value to YasonFloat              |
| float                               | getFloat(String key)                                | Retrieves the float value based on the key, attempts type conversion         |
| float                               | getFloat(String key, float defaultValues)          | Retrieves the float value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, double value)                       | Adds a key-value pair, converts double type value to YasonDouble            |
| double                              | getDouble(String key)                               | Retrieves the double value based on the key, attempts type conversion        |
| double                              | getDouble(String key, double defaultValues)        | Retrieves the double value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, byte[] value)                       | Adds a key-value pair, converts byte[] type value to YasonBinary           |
| byte[]                              | getBytes(String key)                                | Retrieves the byte[] value based on the key          |
| byte[]                              | getBytes(String key, byte[] defaultValues)         | Retrieves the byte[] value based on the key, returns default value if key doesn't exist or type mismatch occurs |
| YasonValue                          | put(String key, Date value)                         | Adds a key-value pair, converts Date type value to YasonDate                |
| Date                                | getDate(String key)                                 | Retrieves the Date value based on the key, attempts type conversion         |
| Date                                | getDate(String key, Date defaultValues)            | Retrieves the Date value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, LocalDate value)                    | Adds a key-value pair, converts LocalDate type value to YasonDate           |
| LocalDate                           | getLocalDate(String key)                            | Retrieves the LocalDate value based on the key, attempts type conversion     |
| LocalDate                           | getLocalDate(String key, LocalDate defaultValues)  | Retrieves the LocalDate value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, LocalTime value)                    | Adds a key-value pair, converts LocalTime type value to YasonTime           |
| LocalTime                           | getLocalTime(String key)                            | Retrieves the LocalTime value based on the key, attempts type conversion     |
| LocalTime                           | getLocalTime(String key, LocalTime defaultValues)  | Retrieves the LocalTime value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, Time value)                         | Adds a key-value pair, converts Time type value to YasonTime                |
| Time                                | getTime(String key)                                 | Retrieves the Time value based on the key, attempts type conversion          |
| Time                                | getTime(String key, Time defaultValues)            | Retrieves the Time value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, Timestamp value)                    | Adds a key-value pair, converts Timestamp type value to YasonTimestamp       |
| Timestamp                           | getTimestamp(String key)                            | Retrieves the Timestamp value based on the key, attempts type conversion     |
| Timestamp                           | getTimestamp(String key, Timestamp defaultValues)   | Retrieves the Timestamp value based on the key, returns default value if key doesn't exist or type conversion fails |
| YasonValue                          | put(String key, LocalDateTime value)                | Adds a key-value pair, converts LocalDateTime type value to YasonTimestamp  |
| LocalDateTime                       | getLocalDateTime(String key)                        | Retrieves the LocalDateTime value based on the key, attempts type conversion |
| LocalDateTime                       | getLocalDateTime(String key, LocalDateTime defaultValues) | Retrieves the LocalDateTime value based on the key, returns default value if key doesn't exist or type conversion fails |
| int                                 | size()                                             | Gets the count of key-value pairs in the jsonObject  |
| boolean                             | isEmpty()                                          | Checks if it is empty                                  |
| boolean                             | containsKey(Object key)                            | Checks if a specific key exists                        |
| boolean                             | containsValue(Object value)                        | Checks if a specific value exists                      |
| YasonValue                          | remove(Object key)                                 | Removes a key from the JSON                             |
| void                                | putAll(Map<? extends String, ? extends YasonValue> m) | Adds all key-value pairs from the provided map to the JSON |
| void                                | clear()                                            | Clears the JSON                                       |
| Collection<YasonValue>             | values()                                           | Gets all values in the JSON                           |
| Set<Map.Entry<String, YasonValue>> | entrySet()                                         | Gets all key-value pairs in the JSON                   |

Example of using YasonObject:

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

        System.out.println(json.getInt("key1", 2)); // 2, the value of key1 is "value1", not an int type, so getInt returns the default value
        System.out.println(json.getString("key6", "defaultStrValue")); // defaultStrValue, key6 does not exist in json, so return default value

        YasonObject json1 = new YasonObject();
        json1.put("key6", json); // A YasonObject can be used as a value in another YasonObject
        System.out.println(((YasonObject)json1.get("key6")).getString("key1")); // {"key6":{"key1":"value1","key2":34.3,"key3":2,"key4":"616161","key5":"2023-08-30T00:00:00"}}
        
        json.clear();
        System.out.println(json.size()); // 0
```
