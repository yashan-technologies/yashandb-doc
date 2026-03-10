The Savepoint is used to represent a savepoint object, which can be either named or unnamed.

The YashanDB JDBC driver supports the Savepoint interface functionality:

|Return Type |Method |Remarks |
|----------------------|--------------------|-----------------------|
| int                 | getSavepointId()      | Only unnamed savepoints can call this; otherwise, an exception will be thrown. |
| String              | getSavepointName()    | Only named savepoints can call this; otherwise, an exception will be thrown. |