YashanDB Python驱动会对YashanDB数据类型进行转换，转换关系如下：

- 绑定输入

  | Python Type        | YashanDB Type          |
  | ------------------ | ---------------------- |
  | int                | BigInt                 |
  | bytes              | Raw                    |
  | bool               | Boolean                |
  | datetime.date      | Date                   |
  | float              | Double                 |
  | datetime.timedelta | INTERVAL DAY TO SECOND |
  | datetime.time      | Time                   |
  | datetime.datetime  | TimeStamp              |
  | str                | Varchar                |
  | decimal.Decimal    | Varchar                |
  | array | Vector |
  
- 查询输出

  | YashanDB Type          | Python Type        |
  | ---------------------- | ------------------ |
  | Boolean                | bool               |
  | TinyInt                | int                |
  | SmallInt               | int                |
  | Int                    | int                |
  | BigInt                 | int                |
  | Float                  | float              |
  | Double                 | float              |
  | Number                 | decimal.Decimal    |
  | Date                   | datetime.date      |
  | Time                   | datetime.time      |
  | TimeStamp              | datetime.datetime  |
  | Char/Varchar           | str                |
  | Raw                    | bytes              |
  | RowId                  | str                |
  | URowId                 | str                |
  | Clob                   | str                |
  | Blob                   | bytes              |
  | Bit                    | str                |
  | INTERVAL DAY TO SECOND | datetime.timedelta |
  | INTERVAL YEAR TO MONTH | str                |
  | Vector  | array |
