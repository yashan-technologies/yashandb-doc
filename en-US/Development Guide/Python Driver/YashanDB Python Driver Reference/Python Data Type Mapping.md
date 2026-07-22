The YashanDB Python driver will convert YashanDB data types as follows:

## Input Binding

  | YashanDB Type          | Python Type        |
  | ---------------------- | ------------------ |
  | BigInt                 | int                |
  | Binary                 | bytes              |
  | Double                 | float              |
  | Boolean                | bool               |
  | Date                   | datetime.date      |
  | Float                  | float              |
  | Int                    | int                |
  | INTERVAL DAY TO SECOND | datetime.timedelta |
  | JSON                   | str or dict        |
  | NCLOB                  | str                |
  | SmallInt               | int                |
  | Time                   | datetime.time      |
  | TimeStamp              | datetime.datetime   |
  | TimeStamp WITH TIME ZONE | datetime.datetime |
  | TinyInt                | int                |
  | Varchar/Char           | str                |
  | Number                 | decimal.Decimal    |
  | Vector                 | array              |

## Query Output

  | YashanDB Type               | Python Type        |
  | --------------------------- | ------------------ |
  | Boolean                    | bool               |
  | TinyInt                    | int                |
  | SmallInt                   | int                |
  | Int                        | int                |
  | BigInt                     | int                |
  | Float                      | float              |
  | Double                     | float              |
  | Number                     | decimal.Decimal    |
  | Date                       | datetime.date      |
  | Time                       | datetime.time      |
  | TimeStamp                  | datetime.datetime  |
  | TimeStamp WITH TIME ZONE   | datetime.datetime  |
  | Char/Varchar               | str                |
  | Raw                        | bytes              |
  | RowId                      | str                |
  | URowId                     | str                |
  | Clob                       | str                |
  | Blob                       | bytes              |
  | JSON                       | dict / list / str  |
  | NCLOB                      | str                |
  | Bit                        | str                |
  | INTERVAL DAY TO SECOND     | datetime.timedelta |
  | INTERVAL YEAR TO MONTH     | str                |
  | Vector                     | array              |

> **Note**：
>
> When binding Vector type, Python's array module must be used with specified serialization format. Query output directly returns an array.array object, which can be used directly without additional parsing.

```python
import array

# Binding input example
vec = array.array('f', [1.0, 2.0, 3.0])  # Vector of float type

# Query output example
cursor.execute("SELECT vec_col FROM vec_table")
result = cursor.fetchone()
if result:
    vec_data = result[0]  # Directly get array.array object, no need to re-wrap
    print(vec_data)
```
