The classes and interfaces in the com.yashandb.json package are used for handling SQL JSON types. This package allows you to:

- Store and retrieve JSON type values in the database.
- Read, create, and modify JSON type values.
- Encode/decode JSON type values in the same binary JSON storage format used by the database.
- Convert JSON type values to JSON text and convert JSON text back to JSON type values.

The com.yashandb.json package consists of the following three parts:

|Description |Interfaces |
|--------------------------------|--------------------------------|
| JSON type object model               | YasonValue, YasonObject, YasonArray, YasonString, YasonDecimal,<br/>YasonBoolean, YasonNull, YasonByte, YasonShort, YasonInt,<br/>YasonLong, YasonFloat, YasonDouble, YasonBinary,<br/>YasonDate, YasonTime, YasonTimestamp |
| JSON type event stream reader and writer | YasonGenerator, YasonParser               |
| Factory for reading, writing, and creating JSON type values |  YasonFactory                 |

The interfaces in this module are not suitable for ISC Distributed Cluster Deployment.