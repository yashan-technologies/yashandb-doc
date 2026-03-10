The YasonDB JDBC driver defines the YasonTimestamp object model to represent SCN data in JSON. It is a wrapper type for Java's Timestamp. Typically, it is used as a value in YasonObject or YasonArray, but it can also exist independently.

It includes two constructors:

|Method |Remarks |
|----------------------------------------------|-----------------------------------|
| YasonTimestamp(Timestamp value)                  | Creates a YasonTimestamp object with the specified SCN value      |
| YasonTimestamp(LocalDateTime localDateTime)      | Creates a YasonTimestamp object based on localDateTime |

It includes the following methods:

|Return Type |Method |Remarks |
|---------------|----------------------|---------------------|
| Timestamp           | getTimestamp()          | Retrieves the value as Timestamp type     |
| Timestamp           | TimestampValue()        | Retrieves the value as Timestamp type     |
| LocalDateTime       | getLocalDateTime()      | Retrieves the value as LocalDateTime type |
| LocalDateTime       | localDateTimeValue()    | Retrieves the value as LocalDateTime type |
 
```java

        YasonTimestamp timestamp = new YasonTimestamp(Timestamp.valueOf("2022-08-12 15:35:55.444555"));
        System.out.println(timestamp.getLocalDateTime()); // 2022-08-12T15:35:55.444555
        
        YasonTimestamp timestamp1 = new YasonTimestamp(LocalDateTime.of(2022,8,12,15,35,55,444555000));
        System.out.println(timestamp1.getTimestamp()); // 2022-08-12 15:35:55.444555
        
        System.out.println(timestamp1.equals(timestamp)); //true

```
