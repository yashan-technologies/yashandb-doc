YashanDB JDBC driver defines seven object models representing seven different specifications of data. Below are the definitions of these seven types of models.

|Class |Corresponding Java Type |Meaning |
|--------------|------------|-------------------------------|
| YasonDecimal  | BigDecimal              | Can represent all numeric types; the numeric type of json  | 
| YasonByte     | byte                    | Represents integers within the byte range; a subdivision of the json numeric type  |
| YasonShort    | short                   | Represents integers within the short range; a subdivision of the json numeric type  |
| YasonInt      | int                     | Represents integers within the int range; a subdivision of the json numeric type  |
| YasonLong     | long                    | Represents integers within the long range; a subdivision of the json numeric type  |
| YasonFloat    | float                   | Represents numbers within the float range; a subdivision of the json numeric type  |
| YasonDouble   | double                  | Represents numbers within the double range; a subdivision of the json numeric type  |

The constructors for each type are as follows:

|Class |Constructor |
|-------------------------------------------|------------------------------------------|
| YasonDecimal              | * YasonDecimal(BigDecimal value)<br/>* YasonDecimal(Number value)                                |
| YasonByte                     | YasonByte(byte value)                               |
| YasonShort                   | YasonShort(short value)                               |
| YasonInt                       | YasonInt(int value)                               |
| YasonLong                   | YasonLong(long value)                               |
| YasonFloat                   | YasonFloat(float value)                               |
| YasonDouble                | YasonDouble(double value)                               |

All types support the same methods.

|Return Type |Method |Note |
|------------|-------------------|------------------|
| BigDecimal       | getBigDecimal()     | Get the value as BigDecimal  |
| BigDecimal       | BigDecimalValue()   | Get the value as BigDecimal  |
| byte             | getByte()           | Get the value as byte        |
| byte             | byteValue()         | Get the value as byte        |
| short            | getShort()          | Get the value as short       |
| short            | shortValue()        | Get the value as short       |
| int              | getInt()            | Get the value as int         |
| int              | intValue()          | Get the value as int         |
| long             | getLong()           | Get the value as long        |
| long             | longValue()         | Get the value as long        |
| float            | getFloat()          | Get the value as float       |
| float            | floatValue()        | Get the value as float       |
| double           | getDouble()         | Get the value as double      |
| double           | doubleValue()       | Get the value as double      |

An example of usage is as follows:
```java
        YasonDecimal decimal = new YasonDecimal(new BigDecimal("300.000"));
        System.out.println(decimal.getInt());// 300
        System.out.println(decimal.getFloat());// 300.0

        YasonByte yasonByte = new YasonByte((byte) 3);
        System.out.println(yasonByte.getInt());// 3
        System.out.println(yasonByte.getBigDecimal());// 3

        YasonDouble yasonDouble = new YasonDouble(23.323);
        System.out.println(yasonDouble.getInt());// 23
        System.out.println(yasonDouble.getBigDecimal());// 23.323

        // As a value of YasonObject
        YasonObject object = new YasonObject();
        object.put("key1",decimal);
        object.put("key2",yasonByte);
        object.put("key3",yasonDouble);
        System.out.println(object);// {"key1":300.000,"key2":3,"key3":23.323}

```
