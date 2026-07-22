YashanDB JDBC驱动里面定义了YasonArray对象模型，用来表示JSON数组类型的数据，其中value为YasonValue类型，value之间是有序的。

它包含两个构造方法：

|  方法| 备注|
|--------------------------------------|--------------------------------------------------|
| YasonArray()                        | 创建一个空的JsonArray对象                                |
| YasonArray(YasonArray yasonArray) | 创建一个JsonArray对象，并且把入参的yasonArray的value对全部加到这个对象里 |

它包含如下普通方法：

|  返回类型| 方法| 备注|
|--------------------------|-------------------------------------------------------|------------------------------------------------|
| int                      | size()                                                | 获取Array长度                                      |
| boolean                  | isEmpty()                                             | 是否为空                                           |
| boolean                  | contains(Object o)                                    | 是否包含某元素                                        |
| Object[]                 | toArray()                                             | 转化为数组类型                                        |
| <T> T[]                  | toArray(T[] a)                                        | 转化为指定类型的数组                                     |
| boolean                  | remove(Object o)                                      | 移除某元素                                          |
| boolean                  | remove(int intex)                                     | 移除对应下标的元素                                      |
| boolean                  | containsAll(Collection<?> c)                          | 是否包含所有元素                                       |
| boolean                  | addAll(Collection<? extends YasonValue> c)            | 在末尾添加全部元素                                      |
| boolean                  | addAll(int index, Collection<? extends YasonValue> c) | 在指定位置添加全部元素                                    |
| boolean                  | removeAll(Collection<?> c)                            | 移除入参里的全部元素                                     |
| boolean                  | retainAll(Collection<?> c)                            | 移除除了入参里的元素之外的元素                                |
| int                      | indexOf(Object o)                                     | 获取元素下标                                         |
| int                      | lastIndexOf(Object o)                                 | 获取元素的最后一个下标                                    |
| Iterator<YasonValue>     | iterator()                                            | 获取迭代器                                          |
| ListIterator<YasonValue> | listIterator()                                        | 获取List迭代器                                      |
| ListIterator<YasonValue> | listIterator(int index)                               | 获取指定下标开始的List迭代器                               |
| boolean                  | add(YasonValue element)                               | 在末尾追加一个元素                                      |
| void                     | add(int index, YasonValue element)                    | 在中间插入一个元素                                      |
| YasonValue               | set(int index, YasonValue element)                    | 替换指定下标的值                                      |
| YasonValue               | get(int index)                                        | 根据下标取值                                         |
| boolean                  | add(String element)                                   | 把入参String类型的值转化成YasonString追加到末尾               |
| void                     | add(int index, String element)                        | 把入参String类型的值转化成YasonString插入到指定位置             |
| YasonValue               | set(int index, String element)                        | 把入参String类型的值转化成YasonString替换指定下标的值           |
| String                   | getString(int index)                                  | 根据下标获取String类型的值                               |
| boolean                  | add(BigDecimal element)                               | 把入参BigDecimal类型的值转化成YasonDecimal追加到末尾          |
| void                     | add(int index, BigDecimal element)                    | 把入参BigDecimal类型的值转化成YasonDecimal插入到指定位置        |
| YasonValue               | set(int index, BigDecimal element)                    | 把入参BigDecimal类型的值转化成YasonDecimal替换指定下标的值      |
| BigDecimal               | getBigDecimal(int index)                              | 根据下标获取BigDecimal类型的值                           |
| boolean                  | add(boolean element)                                  | 把入参boolean类型的值转化成YasonBoolean追加到末尾             |
| void                     | add(int index, boolean element)                       | 把入参boolean类型的值转化成YasonBoolean插入到指定位置           |
| YasonValue               | set(int index, boolean element)                       | 把入参boolean类型的值转化成YasonBoolean替换指定下标的值         |
| boolean                  | getBoolean(int index)                                 | 根据下标获取boolean类型的值                              |
| boolean                  | addNull()                                             | 把YasonNull追加到末尾                                |
| void                     | addNull(int index)                                    | 把YasonNull插入到指定位置                              |
| YasonValue               | setNull(int index)                                    | 把YasonNull替换指定下标的值                            |
| boolean                  | isNull(int index)                                     | 判断给定的下标的值是否为YasonNull                          |
| boolean                  | add(byte element)                                     | 把入参byte类型的值转化成YasonByte追加到末尾                   |
| void                     | add(int index, byte element)                          | 把入参byte类型的值转化成YasonByte插入到指定位置                 |
| YasonValue               | set(int index, byte element)                          | 把入参byte类型的值转化成YasonByte替换指定下标的值               |
| byte                     | getByte(int index)                                    | 根据下标获取byte类型的值                                 |
| boolean                  | add(short element)                                    | 把入参short类型的值转化成YasonShort追加到末尾                 |
| void                     | add(int index, short element)                         | 把入参short类型的值转化成YasonShort插入到指定位置               |
| YasonValue               | set(int index, short element)                         | 把入参short类型的值转化成YasonShort替换指定下标的值             |
| short                    | getShort(int index)                                   | 根据下标获取short类型的值                                |
| boolean                  | add(int element)                                      | 把入参int类型的值转化成YasonInt追加到末尾                     |
| void                     | add(int index, int element)                           | 把入参int类型的值转化成YasonInt插入到指定位置                   |
| YasonValue               | set(int index, int element)                           | 把入参int类型的值转化成YasonInt替换指定下标的值                 |
| int                      | getInt(int index)                                     | 根据下标获取int类型的值                                  |
| boolean                  | add(long element)                                     | 把入参long类型的值转化成YasonLong追加到末尾                   |
| void                     | add(int index, long element)                          | 把入参long类型的值转化成YasonLong插入到指定位置                 |
| YasonValue               | set(int index, long element)                          | 把入参long类型的值转化成YasonLong替换指定下标的值               |
| long                     | getLong(int index)                                    | 根据下标获取long类型的值                                 |
| boolean                  | add(float element)                                    | 把入参float类型的值转化成YasonFloat追加到末尾                 |
| void                     | add(int index, float element)                         | 把入参float类型的值转化成YasonFloat插入到指定位置               |
| YasonValue               | set(int index, float element)                         | 把入参float类型的值转化成YasonFloat替换指定下标的值             |
| float                    | getFloat(int index)                                   | 根据下标获取float类型的值                                |
| boolean                  | add(double element)                                   | 把入参double类型的值转化成YasonDouble追加到末尾               |
| void                     | add(int index, double element)                        | 把入参double类型的值转化成YasonDouble插入到指定位置             |
| YasonValue               | set(int index, double element)                        | 把入参double类型的值转化成YasonDouble替换指定下标的值           |
| double                   | getDouble(int index)                                  | 根据下标获取double类型的值                               |
| boolean                  | add(byte[] element)                                   | 把入参byte[]类型的值转化成YasonBinary追加到末尾               |
| void                     | add(int index, byte[] element)                        | 把入参byte[]类型的值转化成YasonBinary插入到指定位置             |
| YasonValue               | set(int index, byte[] element)                        | 把入参byte[]类型的值转化成YasonBinary替换指定下标的值           |
| byte[]                   | getBytes(int index)                                   | 根据下标获取byte[]类型的值                               |
| boolean                  | add(Date element)                                     | 把入参Date类型的值转化成YasonDate追加到末尾                   |
| void                     | add(int index, Date element)                          | 把入参Date类型的值转化成YasonDate插入到指定位置                 |
| YasonValue               | set(int index, Date element)                          | 把入参Date类型的值转化成YasonDate替换指定下标的值               |
| Date                     | getDate(int index)                                    | 根据下标获取Date类型的值                                 |
| boolean                  | add(LocalDate element)                                | 把入参LocalDate类型的值转化成YasonDate追加到末尾              |
| void                     | add(int index, LocalDate element)                     | 把入参LocalDate类型的值转化成YasonDate插入到指定位置            |
| YasonValue               | set(int index, LocalDate element)                     | 把入参LocalDate类型的值转化成YasonDate替换指定下标的值          |
| LocalDate                | getLocalDate(int index)                               | 根据下标获取LocalDate类型的值                            |
| boolean                  | add(LocalDateTime element)                            | 把入参LocalDateTime类型的值转化成YasonTimestamp追加到末尾     |
| void                     | add(int index, LocalDateTime element)                 | 把入参LocalDateTime类型的值转化成YasonTimestamp插入到指定位置   |
| YasonValue               | set(int index, LocalDateTime element)                 | 把入参LocalDateTime类型的值转化成YasonTimestamp替换指定下标的值 |
| LocalDateTime            | getLocalDateTime(int index)                           | 根据下标获取LocalDateTime类型的值                        |
| boolean                  | add(Time element)                                     | 把入参Time类型的值转化成YasonTime追加到末尾                   |
| void                     | add(int index, Time element)                          | 把入参Time类型的值转化成YasonTime插入到指定位置                 |
| YasonValue               | set(int index, Time element)                          | 把入参Time类型的值转化成YasonTime替换指定下标的值               |
| Time                     | getTime(int index)                                    | 根据下标获取Time类型的值                                 |
| boolean                  | add(LocalTime element)                                | 把入参LocalTime类型的值转化成YasonTime追加到末尾              |
| void                     | add(int index, LocalTime element)                     | 把入参LocalTime类型的值转化成YasonTime插入到指定位置            |
| YasonValue               | set(int index, LocalTime element)                     | 把入参LocalTime类型的值转化成YasonTime替换指定下标的值          |
| LocalTime                | getLocalTime(int index)                               | 根据下标获取LocalTime类型的值                            |
| boolean                  | add(Timestamp element)                                | 把入参Timestamp类型的值转化成YasonTimestamp追加到末尾         |
| void                     | add(int index, Timestamp element)                     | 把入参Timestamp类型的值转化成YasonTimestamp插入到指定位置       |
| YasonValue               | set(int index, Timestamp element)                     | 把入参Timestamp类型的值转化成YasonTimestamp替换指定下标的值     |
| Timestamp                | getTimestamp(int index)                               | 根据下标获取Time类型的值                                 |

## YasonArray使用示例如下：

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
