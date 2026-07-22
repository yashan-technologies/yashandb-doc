The YashanDB JDBC driver defines the YasonBinary object model to represent binary data within JSON, which is a wrapper type for java's byte[]. It is typically used as a value of YasonObject or YasonArray but can also exist independently.

The constructor is as follows:
YasonBinary(byte[] value)

It includes the following common methods:

|Return Type |Method |Remarks |
|---------|--------------|-------------|
| byte[]      | getBytes()     | Retrieves the byte[] value |
| byte[]      | bytesValue()   | Retrieves the byte[] value |

Note that its toString() method converts the byte array into hexadecimal format.

```java
        YasonBinary binary = new YasonBinary(new byte[]{97,97,-1});
        byte[] bytes = binary.getBytes();
        System.out.println(Arrays.toString(bytes)); // [97,97,-1]
        System.out.println(binary); // "6161FF", converted to hexadecimal

```
