The YasonDB JDBC driver defines the YasonString object model to represent string data in JSON. It is a wrapper type for Java's String. It is generally used as a value for YasonObject or YasonArray, but it can also exist independently.

It includes two constructors:

|Method |Note |
|---------------------------|-----------------------------------|
| YasonString(String value)    | Creates a YasonString object with the specified string value |
| YasonString(Object value)    | Creates a YasonString object with the value of the specified object's toString |

It includes the following common methods:

|Return Type |Method |Note |
|---------|---------------|-------------|
| String      | getString()       | Gets the String value |
| String      | stringValue()     | Gets the String value |

```java
        YasonString yasonString = new YasonString("stringValue");
        System.out.println(yasonString.getString()); // stringValue
        System.out.println(yasonString); // "stringValue", the String type in JSON must be enclosed in double quotes

        // Used as a value for YasonObject
        YasonObject json = new YasonObject();
        json.put("key",yasonString);
        System.out.println(json); // {"key":"stringValue"}

```
