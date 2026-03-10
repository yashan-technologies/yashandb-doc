The YashanDB JDBC driver defines the YasonTime object model to represent time data in JSON. It is a wrapper type for java.sql.Time. It is typically used as a value of a YasonObject or YasonArray, but it can also exist independently.

It includes the following two constructors:

|Method |Note |
|--------------------------------------|--------------------------|
| YasonTime(Time value)           | Creates a YasonTime object with the specified date value.   |
| YasonTime(LocalTime localTime)  | Creates a YasonTime object based on localTime. |

It includes the following methods:

|Return Type |Method |Note |
|-----------|------------------|-----------------|
| Time      | getTime()        | Gets the value as Time type.      |
| Time      | TimeValue()      | Gets the value as Time type.      |
| LocalTime | getLocalTime()   | Gets the value as LocalTime type. |
| LocalTime | localTimeValue() | Gets the value as LocalTime type. |
 
```java

        YasonTime yasonTime = new YasonTime(LocalTime.of(15,35,55,434000000));
        System.out.println(yasonTime.getLocalTime()); // 15:35:55.434
        System.out.println(yasonTime.getTime()); // 15:35:55.434000

        YasonObject json = new YasonObject();
        json.put("time1",yasonTime);
        System.out.println(json); // {"time1":"15:35:55.434"}

```
