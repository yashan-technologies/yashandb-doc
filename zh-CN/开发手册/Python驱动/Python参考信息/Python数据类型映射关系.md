YashanDB Python驱动会对YashanDB数据类型进行转换，转换关系如下：

## 绑定输入

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
  | JSON                   | str 或 dict        |
  | NCLOB                  | str                |
  | SmallInt               | int                |
  | Time                   | datetime.time      |
  | TimeStamp              | datetime.datetime   |
  | TimeStamp WITH TIME ZONE | datetime.datetime |
  | TinyInt                | int                |
  | Varchar/Char           | str                |
  | Number                 | decimal.Decimal    |
  | Vector                 | array              |

## 查询输出

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
> Vector类型绑定时需使用Python的array模块，并按指定格式序列化。查询输出时直接返回array.array对象，可直接使用。

```python
import array

# 绑定输入示例
vec = array.array('f', [1.0, 2.0, 3.0])  # float类型的Vector

# 查询输出示例
cursor.execute("SELECT vec_col FROM vec_table")
result = cursor.fetchone()
if result:
    vec_data = result[0]  # 直接得到 array.array 对象，无需二次封装
    print(vec_data)
```
