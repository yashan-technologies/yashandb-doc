YashanDB JDBC driver has the capability to read stream objects and parse them into JSON (stream reader) and to write JSON strings into stream objects (stream writer).

### Stream Reader YasonParser

The YasonParser parses the JSON strings contained within stream objects (Reader or InputStream) by gradually reading them to produce a complete JSON object or to extract specific data from the JSON.

YasonParser objects are typically obtained through the following two methods in the YasonFactory class:

|Method |Remarks |
|------------------------------------------|--------------------------------|
| createJsonTextParser(java.io.Reader)     | Parse the JSON string read from the Reader object      |
| createJsonTextValue(java.io.InputStream) | Parse the JSON string read from the InputStream object |

The operation interface for YasonParser is as follows:

|Method |Remarks |
|-----------------------------|-----------------------------------------------------------|
| boolean hasNext()           | Check if there is another element                                |
| Event next()                | Read the next element; the return value Event represents the type of the next element read, see the YasonParser.Event type introduction section below |
| String getString()          | Retrieve the current string read; an error is reported if the read value is neither a key nor a string or a number |
| boolean isIntegralNumber()  | Determine if the currently parsed number is an integer; an error is reported if it is not a number type |
| int getInt()                | Retrieve the int value of the currently read number; an error is reported if the read value is not a number |
| long getLong()              | Retrieve the long value of the currently read number; an error is reported if the read value is not a number |
| double getDouble()          | Retrieve the double value of the currently read number; an error is reported if the read value is not a number |
| float getFloat()            | Retrieve the float value of the currently read number; an error is reported if the read value is not a number |
| BigInteger getBigInteger()  | Retrieve the BigInteger value of the currently read number; an error is reported if the read value is not a number |
| BigDecimal getBigDecimal()  | Retrieve the BigDecimal value of the currently read number; an error is reported if the read value is not a number |
| YasonValue getValue()       | Retrieve the currently read YasonValue |
| YasonArray getArray()       | Retrieve the currently read YasonArray; an error is reported if the type does not match |
| YasonObject getObject()     | Retrieve the currently read YasonObject; an error is reported if the type does not match |
| void skipArray()            | Skip the next YasonArray value; ignore if the type is not YasonArray |
| void skipObject()           | Skip the next YasonObject value; ignore if the type is not YasonObject |
| void close()                | Close the stream in the parameter and stop parsing the JSON string |

#### YasonParser.Event Type Introduction

YasonParser.Event is an enum type used to describe the type of the current element parsed from the stream, with the following values:

|Enum Value |Meaning |
|---------------|--------------------------------|
| START_ARRAY      | Reached the start symbol of an Array, i.e., the left bracket `[` in the JSON string |
| START_OBJECT     | Reached the start of an OBJECT, i.e., the left curly brace `{` in the JSON string   |
| KEY_NAME         | Reached the key of an OBJECT                 |
| VALUE_STRING     | Reached a String type value                    |
| VALUE_DECIMAL    | Reached a numeric type value                   |
| VALUE_TRUE       | Reached the boolean value true                 |
| VALUE_FALSE      | Reached the boolean value false                |
| VALUE_NULL       | Reached the null value in JSON                 |
| END_OBJECT       | Reached the end of an OBJECT, i.e., the right curly brace `}` in the JSON string    |
| END_ARRAY        | Reached the end of an Array, i.e., the right bracket `]` in the JSON string        |

#### YasonParser Usage Example

***Example 1***: This example demonstrates how to parse each value from a stream object that contains a JSON string:
```java
        YasonFactory factory = new YasonFactory();
        String json = "{\"key1\":\"value1\",\"key2\":false,\"key3\":[\"value2\",33,true,null],\"key4\":false,\"\":23}";
        
        YasonParser parser = factory.createJsonTextParser(new StringReader(json));
        while (parser.hasNext()) {
            YasonParser.Event event = parser.next();
            System.out.println(event);
            switch (event) {
                case KEY_NAME:
                case VALUE_STRING:
                case VALUE_DECIMAL:
                    System.out.println(parser.getString());
                    break;
                case VALUE_FALSE:
                case VALUE_TRUE:
                case VALUE_NULL:
                    System.out.println(parser.getValue());
                default:
            }
        }
       
```

***Example 2***: This example demonstrates how to parse a complete JSON object from a stream object that contains a JSON string:
```java
        YasonFactory factory = new YasonFactory();
        String json = "{\"key1\":\"value1\",\"key2\":false,\"key3\":[\"value2\",33,true,null],\"key4\":false,\"key5\":23}";
        YasonParser parser = factory.createJsonTextParser(new StringReader(json));
        System.out.println(parser.hasNext()); // true
        System.out.println(parser.next()); // START_OBJECT, parsing json results in type START_OBJECT
        YasonObject object = parser.getObject(); // Directly obtain the YasonObject through the getObject interface
        
        // Various operations can be performed on the parsed YasonObject
        System.out.println(object); // {"key1":"value1","key2":false,"key3":["value2",33,true,null],"key4":false,"key5":23}
        System.out.println(object.get("key1")); // "value1"
        System.out.println(object.get("key3").getType()); // YASON_ARRAY
        System.out.println(object.get("key3")); // ["value2",33,true,null]
       
```

### Stream Writer YasonGenerator

The YasonGenerator generates a JSON string and writes it into a stream object by passing in keys, values, or objects.

YasonGenerator objects are typically obtained through the following two methods in the YasonFactory class:

|Method |Remarks |
|------------------------------------------|----------------------------------|
| createJsonTextGenerator(Writer var1)     | Create a YasonGenerator that writes the generated JSON string into the given Writer object |
| createJsonTextGenerator(OutputStream var1) | Create a YasonGenerator that writes the generated JSON string into the given OutputStream object |

The operation interface for YasonGenerator is as follows:

|Method |Meaning |
|------------------------------------------------------|-------------------------------------------------------------------------------------|
| YasonGenerator writeStartObject()                    | Write the start symbol of an Object to the stream, i.e., the left curly brace `{`      |
| YasonGenerator writeStartArray()                     | Write the start symbol of an Array to the stream, i.e., the left bracket `[`           |
| YasonGenerator writeKey(String value)                | Write an object key value to the stream                                                 |
| YasonGenerator write(YasonValue value)               | Write a YasonValue type value to the stream                                             |
| YasonGenerator write(String value)                   | Write a String type value to the stream                                                 |
| YasonGenerator write(Number value)                   | Write a Number type value (common int, double, BigDecimal, etc.) to the stream        |
| YasonGenerator write(boolean value)                  | Write a boolean type value to the stream                                                |
| YasonGenerator write(LocalDateTime value)            | Write a LocalDateTime type value to the stream                                         |
| YasonGenerator write(byte[] value)                   | Write a byte[] type value to the stream                                                |
| YasonGenerator writeNull()                           | Write a null value to the stream                                                        |
| YasonGenerator writeStartObject(String key)          | Write a key, then write the start symbol of an Object                                   |
| YasonGenerator writeStartArray(String key)           | Write a key, then write the start symbol of an Array                                    |
| YasonGenerator write(String key,YasonValue value)    | Write a key, then write a YasonValue type value                                         |
| YasonGenerator write(String key,String value)        | Write a key, then write a String type value                                             |
| YasonGenerator write(String key,Number value)        | Write a key, then write a Number type value                                             |
| YasonGenerator write(String key,boolean value)       | Write a key, then write a boolean type value                                            |
| YasonGenerator write(String key,LocalDateTime value) | Write a key, then write a LocalDateTime type value                                      |
| YasonGenerator write(String key,byte[] value)        | Write a key, then write a byte[] type value                                             |
| YasonGenerator writeNull(String key)                 | Write a key, then write a null value                                                    |
| YasonGenerator writeEnd()                            | Write an end symbol; if the current object is not closed, it will write the end symbol (right curly brace `}`) for the Object, and if the current Array is not closed, it will write the end symbol (right bracket `]`) for the Array |
| void close()                                         | Close the stream object                                                                  |
| void flush()                                         | Flush the stream object                                                                  |

>**Note**:
>
> All write methods of YasonGenerator return the YasonGenerator self-object, allowing for method chaining in practical applications.
>
> All write methods should be called correctly; otherwise, exceptions will be thrown.

#### YasonGenerator Usage Example

***Example 1***: This example simply demonstrates how to sequentially write multiple values to eventually generate a JSON string in the output stream:
```java
        YasonFactory factory = new YasonFactory();
        Writer writer = new StringWriter();
        YasonGenerator generator = factory.createJsonTextGenerator(writer);
        generator.writeStartObject();
        generator.writeKey("key1");
        generator.write("value");

        generator.writeKey("key2").write(2).writeKey("key3").write("value3"); // Method chaining

        generator.writeKey("key4");
         // In a JSON object, a key must be followed by a value, so write must be called to write a value; if writeKey or writeEnd is called at this point, an exception will be thrown.
        generator.write(new BigDecimal(32e2));

        YasonArray array = new YasonArray();
        array.add("arrayValue1");
        array.add(3);
        array.add(new byte[]{97,97,97});
        array.add(LocalDateTime.of(2023,8,23,15,34,56));
        generator.write("key5",array);
        generator.writeEnd();
        generator.close();
        // Writing completed, then the generated JSON string can be obtained through writer.toString()
        System.out.println(writer.toString()); //{"key1":"value","key2":2,"key3":"value3","key4":3200,"key5":["arrayValue1",3,"616161","2023-08-23T15:34:56"]}
       
```
