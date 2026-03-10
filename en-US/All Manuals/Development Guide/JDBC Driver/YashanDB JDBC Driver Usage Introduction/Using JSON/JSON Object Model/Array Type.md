The YashanDB JDBC driver defines the YasonArray object model, which is used to represent JSON array type data, where the value is of YasonValue type and the values are ordered.

It contains two constructors:

|Method |Remarks |
|--------------------------------------|--------------------------------------------------|
| YasonArray()                             | Creates an empty JsonArray object                        |
| YasonArray(YasonArray yasonArray)       | Creates a JsonArray object and adds all values from the provided yasonArray to this object |

It includes the following methods:

|Return Type |Method |Remarks |
|--------------------------|-------------------------------------------------------|------------------------------------------------|
| int                      | size()                                             | Gets the length of the Array                       |
| boolean                  | isEmpty()                                          | Checks if the array is empty                       |
| boolean                  | contains(Object o)                                 | Checks if the array contains a specific element    |
| Object[]                 | toArray()                                          | Converts to an array type                           |
| \<T> T[]                 | toArray(T[] a)                                     | Converts to a specified type array                  |
| boolean                  | remove(Object o)                                   | Removes a specific element                          |
| boolean                  | remove(int index)                                  | Removes the element at the specified index         |
| boolean                  | containsAll(Collection\<?> c)                       | Checks if all elements are contained               |
| boolean                  | addAll(Collection\<? extends YasonValue> c)        | Adds all elements to the end                       |
| boolean                  | addAll(int index, Collection\<? extends YasonValue> c) | Adds all elements at the specified position       |
| boolean                  | removeAll(Collection\<?> c)                         | Removes all elements in the provided collection     |
| boolean                  | retainAll(Collection\<?> c)                         | Retains only the elements contained in the provided collection |
| int                      | indexOf(Object o)                                  | Gets the index of the element                      |
| int                      | lastIndexOf(Object o)                              | Gets the last index of the element                 |
| Iterator\<YasonValue>     | iterator()                                         | Gets an iterator                                    |
| ListIterator\<YasonValue> | listIterator()                                     | Gets a List iterator                                |
| ListIterator\<YasonValue> | listIterator(int index)                            | Gets a List iterator starting from the specified index |
| boolean                  | add(YasonValue element)                            | Adds an element to the end                         |
| void                     | add(int index, YasonValue element)                 | Inserts an element at a specified position         |
| YasonValue               | set(int index, YasonValue element)                 | Replaces the value at the specified index          |
| YasonValue               | get(int index)                                     | Gets the value at the specified index              |
| boolean                  | add(String element)                                | Converts the provided String value to YasonString and adds it to the end |
| void                     | add(int index, String element)                     | Converts the provided String value to YasonString and inserts at the specified position |
| YasonValue               | set(int index, String element)                     | Converts the provided String value to YasonString and replaces the value at the specified index |
| String                   | getString(int index)                               | Gets the String value at the specified index       |
| boolean                  | add(BigDecimal element)                            | Converts the provided BigDecimal value to YasonDecimal and adds it to the end |
| void                     | add(int index, BigDecimal element)                 | Converts the provided BigDecimal value to YasonDecimal and inserts at the specified position |
| YasonValue               | set(int index, BigDecimal element)                 | Converts the provided BigDecimal value to YasonDecimal and replaces the value at the specified index |
| BigDecimal               | getBigDecimal(int index)                           | Gets the BigDecimal value at the specified index    |
| boolean                  | add(boolean element)                               | Converts the provided boolean value to YasonBoolean and adds it to the end |
| void                     | add(int index, boolean element)                    | Converts the provided boolean value to YasonBoolean and inserts at the specified position |
| YasonValue               | set(int index, boolean element)                    | Converts the provided boolean value to YasonBoolean and replaces the value at the specified index |
| boolean                  | getBoolean(int index)                              | Gets the boolean value at the specified index      |
| boolean                  | addNull()                                          | Adds YasonNull to the end                         |
| void                     | addNull(int index)                                 | Inserts YasonNull at the specified position        |
| YasonValue               | setNull(int index)                                 | Replaces the value at the specified index with YasonNull |
| boolean                  | isNull(int index)                                  | Checks if the value at the specified index is YasonNull |
| boolean                  | add(byte element)                                  | Converts the provided byte value to YasonByte and adds it to the end |
| void                     | add(int index, byte element)                       | Converts the provided byte value to YasonByte and inserts at the specified position |
| YasonValue               | set(int index, byte element)                       | Converts the provided byte value to YasonByte and replaces the value at the specified index |
| byte                     | getByte(int index)                                 | Gets the byte value at the specified index        |
| boolean                  | add(short element)                                 | Converts the provided short value to YasonShort and adds it to the end |
| void                     | add(int index, short element)                      | Converts the provided short value to YasonShort and inserts at the specified position |
| YasonValue               | set(int index, short element)                      | Converts the provided short value to YasonShort and replaces the value at the specified index |
| short                    | getShort(int index)                                | Gets the short value at the specified index       |
| boolean                  | add(int element)                                   | Converts the provided int value to YasonInt and adds it to the end |
| void                     | add(int index, int element)                        | Converts the provided int value to YasonInt and inserts at the specified position |
| YasonValue               | set(int index, int element)                        | Converts the provided int value to YasonInt and replaces the value at the specified index |
| int                      | getInt(int index)                                  | Gets the int value at the specified index         |
| boolean                  | add(long element)                                  | Converts the provided long value to YasonLong and adds it to the end |
| void                     | add(int index, long element)                       | Converts the provided long value to YasonLong and inserts at the specified position |
| YasonValue               | set(int index, long element)                       | Converts the provided long value to YasonLong and replaces the value at the specified index |
| long                     | getLong(int index)                                 | Gets the long value at the specified index        |
| boolean                  | add(float element)                                 | Converts the provided float value to YasonFloat and adds it to the end |
| void                     | add(int index, float element)                      | Converts the provided float value to YasonFloat and inserts at the specified position |
| YasonValue               | set(int index, float element)                      | Converts the provided float value to YasonFloat and replaces the value at the specified index |
| float                    | getFloat(int index)                                | Gets the float value at the specified index       |
| boolean                  | add(double element)                                | Converts the provided double value to YasonDouble and adds it to the end |
| void                     | add(int index, double element)                     | Converts the provided double value to YasonDouble and inserts at the specified position |
| YasonValue               | set(int index, double element)                     | Converts the provided double value to YasonDouble and replaces the value at the specified index |
| double                   | getDouble(int index)                               | Gets the double value at the specified index      |
| boolean                  | add(byte[] element)                                | Converts the provided byte[] value to YasonBinary and adds it to the end |
| void                     | add(int index, byte[] element)                     | Converts the provided byte[] value to YasonBinary and inserts at the specified position |
| YasonValue               | set(int index, byte[] element)                     | Converts the provided byte[] value to YasonBinary and replaces the value at the specified index |
| byte[]                   | getBytes(int index)                                | Gets the byte[] value at the specified index      |
| boolean                  | add(Date element)                                  | Converts the provided Date value to YasonDate and adds it to the end |
| void                     | add(int index, Date element)                       | Converts the provided Date value to YasonDate and inserts at the specified position |
| YasonValue               | set(int index, Date element)                       | Converts the provided Date value to YasonDate and replaces the value at the specified index |
| Date                     | getDate(int index)                                 | Gets the Date value at the specified index        |
| boolean                  | add(LocalDate element)                             | Converts the provided LocalDate value to YasonDate and adds it to the end |
| void                     | add(int index, LocalDate element)                  | Converts the provided LocalDate value to YasonDate and inserts at the specified position |
| YasonValue               | set(int index, LocalDate element)                  | Converts the provided LocalDate value to YasonDate and replaces the value at the specified index |
| LocalDate                | getLocalDate(int index)                            | Gets the LocalDate value at the specified index   |
| boolean                  | add(LocalDateTime element)                         | Converts the provided LocalDateTime value to YasonTimestamp and adds it to the end |
| void                     | add(int index, LocalDateTime element)              | Converts the provided LocalDateTime value to YasonTimestamp and inserts at the specified position |
| YasonValue               | set(int index, LocalDateTime element)              | Converts the provided LocalDateTime value to YasonTimestamp and replaces the value at the specified index |
| LocalDateTime            | getLocalDateTime(int index)                        | Gets the LocalDateTime value at the specified index |
| boolean                  | add(Time element)                                  | Converts the provided Time value to YasonTime and adds it to the end |
| void                     | add(int index, Time element)                       | Converts the provided Time value to YasonTime and inserts at the specified position |
| YasonValue               | set(int index, Time element)                       | Converts the provided Time value to YasonTime and replaces the value at the specified index |
| Time                     | getTime(int index)                                 | Gets the Time value at the specified index        |
| boolean                  | add(LocalTime element)                             | Converts the provided LocalTime value to YasonTime and adds it to the end |
| void                     | add(int index, LocalTime element)                  | Converts the provided LocalTime value to YasonTime and inserts at the specified position |
| YasonValue               | set(int index, LocalTime element)                  | Converts the provided LocalTime value to YasonTime and replaces the value at the specified index |
| LocalTime                | getLocalTime(int index)                            | Gets the LocalTime value at the specified index   |
| boolean                  | add(Timestamp element)                             | Converts the provided Timestamp value to YasonTimestamp and adds it to the end |
| void                     | add(int index, Timestamp element)                  | Converts the provided Timestamp value to YasonTimestamp and inserts at the specified position |
| YasonValue               | set(int index, Timestamp element)                  | Converts the provided Timestamp value to YasonTimestamp and replaces the value at the specified index |
| Timestamp                | getTimestamp(int index)                            | Gets the Timestamp value at the specified index   |

## Example of YasonArray usage:

```java
        YasonArray jsonArray = new YasonArray();
        jsonArray.add("value1");
        jsonArray.add(3);
        jsonArray.add(new byte[]{97,97,97});
        jsonArray.add(LocalDateTime.of(2023,8,30,12,12,12,623000000));
        YasonArray jsonArray1 = new YasonArray();
        jsonArray1.add("json1value");
        jsonArray.add(jsonArray1);
        System.out.println(Arrays.toString(jsonArray.getBytes(2))); // [97,97,97]
        System.out.println(jsonArray.get(3)); // "2023-08-30T12:12:12.623"

        jsonArray.add(1,"value2");
        System.out.println(jsonArray.get(2)); // 3

        jsonArray.set(3,new BigDecimal("23e1"));
        System.out.println(jsonArray.getDouble(3)); //230.0

        System.out.println(jsonArray); // ["value1","value2",3,230,"2023-08-30T12:12:12.623",["json1value"]]
        System.out.println(jsonArray.size()); // 5
```
