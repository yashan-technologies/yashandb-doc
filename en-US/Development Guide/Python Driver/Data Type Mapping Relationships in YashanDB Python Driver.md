The YashanDB Python driver will convert YashanDB data types as follows:

- Input Binding

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
  
- Query Output

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
