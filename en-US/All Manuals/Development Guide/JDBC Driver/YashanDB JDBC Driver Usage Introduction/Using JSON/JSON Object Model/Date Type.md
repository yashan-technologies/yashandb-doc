The YashanDB JDBC driver defines the YasonDate object model to represent date data within JSON. It is a wrapper type for java.util.Date. It is typically used as a value within a YasonObject or YasonArray, but it can also exist independently.

It includes three constructors:

|Method |Remarks |
|--------------------------------------|--------------------------|
| YasonDate(Date value)                   | Creates a YasonDate object with the specified date value |
| YasonDate(LocalDateTime localDateTime)  | Creates a YasonDate object based on localDateTime |
| YasonDate(LocalDate localDate)          | Creates a YasonDate object based on localDate |

It includes the following methods:

|Return Type |Method |Remarks |
|---------------|----------------------|---------------------|
| Date               | getDate()               | Gets the value of type Date |
| Date               | DateValue()             | Gets the value of type Date |
| LocalDateTime      | getLocalDateTime()      | Gets the value of type LocalDateTime |
| LocalDateTime      | localDateTimeValue()    | Gets the value of type LocalDateTime |
| LocalDate          | getLocalDate()          | Gets the value of type LocalDate |
| LocalDate          | localDateValue()        | Gets the value of type LocalDate |
 
```java

        YasonDate yasonDate = new YasonDate(LocalDateTime.of(2022,8,12,15,35,55));
        System.out.println(yasonDate.getLocalDate()); // 2022-08-12
        System.out.println(yasonDate.getDate()); // Fri Aug 12 15:35:55 CST 2022
        System.out.println(yasonDate.getLocalDateTime()); // 2022-08-12T15:35:55

        // Used as a value in JSON
        YasonObject json = new YasonObject();
        json.put("date1",yasonDate);
        System.out.println(json); // {"date1":"2022-08-12T15:35:55"}

```
