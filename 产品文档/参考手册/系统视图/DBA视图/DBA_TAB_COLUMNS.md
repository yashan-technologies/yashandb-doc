本视图显示所有表的列信息。

| 字段                | 类型            | 说明                                                                                                         |
|-------------------|---------------|------------------------------------------------------------------------------------------------------------|
| OWNER             | VARCHAR(64)   | 该列所属对象的用户名称                                                                                                |
| TABLE_NAME       | VARCHAR(64)   | 该列所属对象名称                                                                                                   |
| COLUMN_NAME      | VARCHAR(64)   | 列名                                                                                                         |
| DATA_TYPE_OWNER | VARCHAR(64)   | 该数据类型所属对象的用户名                                                                                              |
| DATA_TYPE        | VARCHAR(64)   | 列的数据类型                                                                                                     |
| DATA_LENGTH      | INTEGER       | 列的长度（单位：字节）                                                                                                 |
| DATA_PRECISION   | INTEGER       | 列的精度<br>\*   NUMBER类型：十进制有效位数<br>\*   YMInterval类型：年的有效位数<br>\*   DSInterval类型：天的有效位数<br>\*   其他数据类型为NULL  |
| DATA_SCALE       | INTEGER       | 列的范围<br>\*   NUMBER类型：最低有效位所在的精度位置<br>\*   时间类型：小数秒的位数<br>\*   其他数据类型为NULL                                 |
| NULLABLE          | VARCHAR(1)    | 列值是否允许 NULL<br>\*   Y：允许为NULL<br>\*   N：不允许为NULL                                                           |
| COLUMN_ID         | INTEGER       | 列创建的序号                                                                                                     |
| DATA_DEFAULT     | VARCHAR(8000) | 列的默认值                                                                                                      |
| CHAR_USED         | VARCHAR(1) | 列使用BYTE长度语义或CHAR长度语义<br/>\*   C：字符长度语义<br/>\*   B：字节长度语义<br/>\*   NULL:该列不是CHAR/VARCHAR/NCHAR/NVARCHAR数据类型 |
| CHAR_LENGTH       | INTEGER       | 列的字符定义长度<br/>\*   该列是CHAR/VARCHAR/NCHAR/NVARCHAR数据类型时根据定义赋值，其他类型时该列的值为0                                    |
