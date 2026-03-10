YashanDB JDBC driver provides the YasonFactory utility class to obtain defined JSON object models and stream readers and writers.  
The main methods are as follows:

|Method |Meaning |
|-----------------------------------------------------------|----------------------------------------------|
| YasonFactory()                                              | No-argument constructor                          |
| YasonGenerator createJsonTextGenerator(OutputStream var1)  | Creates a YasonGenerator that writes generated JSON strings to the given OutputStream object |
| YasonGenerator createJsonTextGenerator(Writer var1)        | Creates a YasonGenerator that writes generated JSON strings to the given Writer object |
| YasonParser createJsonTextParser(java.io.Reader)           | Creates a YasonParser that reads and parses JSON strings from the Reader object |
| YasonParser createJsonTextParser(java.io.InputStream)      | Creates a YasonParser that reads and parses JSON strings from the InputStream object |
| YasonValue createJsonTextValue(java.io.Reader)             | Parses a YasonValue from the JSON strings in the Reader object |
| YasonValue createJsonTextValue(java.io.InputStream)        | Parses a YasonValue from the JSON strings in the InputStream object |
| YasonObject createObject()                                  | Creates an empty YasonObject                    |
| YasonObject createObject(YasonObject var1)                 | Creates a new YasonObject from the specified YasonObject |
| YasonArray createArray()                                    | Creates an empty YasonArray                     |
| YasonArray createArray(YasonArray var1)                    | Creates a new YasonArray from the specified YasonArray |
| YasonString createString(String var1)                       | Creates a YasonString with the specified String value |
| YasonDecimal createDecimal(String var1)                     | Creates a YasonDecimal with the specified Decimal value |
| YasonInt createInt(int var1)                                | Creates a YasonInt with the specified int value |
| YasonShort createShort(short var1)                          | Creates a YasonShort with the specified short value |
| YasonByte createByte(byte var1)                             | Creates a YasonByte with the specified byte value |
| YasonLong createLong(long var1)                             | Creates a YasonLong with the specified long value |
| YasonFloat createFloat(float var1)                          | Creates a YasonFloat with the specified float value |
| YasonDouble createDouble(double var1)                       | Creates a YasonDouble with the specified double value |
| YasonBoolean createBoolean(boolean var1)                    | Creates a YasonBoolean with the specified boolean value |
| YasonBinary createBinary(byte[] var1)                       | Creates a YasonBinary with the specified byte[] |
| YasonTimestamp createTimestamp(Timestamp var1)              | Creates a YasonTimestamp with the specified Timestamp value |
| YasonTimestamp createTimestamp(LocalDateTime var1)         | Creates a YasonTimestamp with the specified LocalDateTime value |
| YasonDate createDate(Date var1)                             | Creates a YasonDate with the specified Date value |
| YasonDate createDate(LocalDateTime var1)                    | Creates a YasonDate with the specified LocalDateTime value |
| YasonDate createDate(LocalDate var1)                        | Creates a YasonDate with the specified LocalDate value |
| YasonTime createTime(Time var1)                             | Creates a YasonTime with the specified Time value |
| YasonTime createTime(LocalTime var1)                        | Creates a YasonTime with the specified LocalTime value |

### Example of YasonFactory Usage

```java
        YasonFactory factory = new YasonFactory();

        YasonInt yasonInt = factory.createInt(2); // Creates a YasonInt with the value of 2

        YasonObject yasonObject = factory.createObject(); // Creates an empty YasonObject
        yasonObject.put("key1",yasonInt);
        System.out.println(yasonObject); // {"key1":2}

        YasonArray yasonArray = factory.createArray(); // Creates an empty YasonArray
        yasonArray.add(yasonObject);
        yasonArray.add("value2");
        yasonArray.add(yasonInt);
        System.out.println(yasonArray); // [{"key1":2},"value2",2]
        System.out.println(yasonArray.getString(1)); // value2
        
```
