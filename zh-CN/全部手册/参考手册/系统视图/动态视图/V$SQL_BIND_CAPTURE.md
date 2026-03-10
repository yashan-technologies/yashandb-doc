本视图显示所有在库缓存中的绑定变量的相关信息。

|  字段| 类型| 说明|
|--------------------|---------------|---------------------|
| ADDRESS            | RAW(8)        | SQL地址               |
| HASH_VALUE        | BIGINT        | SQL的哈希值，由SQL文本计算得到  |
| SQL_ID            | VARCHAR(13)   | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| CHILD_ADDRESS     | RAW(8)        | 子游标地址               |
| CHILD_NUMBER      | INTEGER       | 子游标编号               |
| NAME               | VARCHAR(64)   | 绑定变量的名称             |
| POSITION           | INTEGER       | 绑定变量在SQL中的位置        |
| DUP_POSITION      | INTEGER       | 如该绑定变量在SQL中有重复使用，则此列的值设置为首个扫描到的绑定变量的位置 |
| DATATYPE           | INTEGER       | 绑定变量数据类型的内部标识符      |
| DATATYPE_STRING    | VARCHAR(32)   | 绑定变量数据类型的文本表示       |
| CHARACTER_SID      | INTEGER       | 国家/地区字符集标识符（保留字段）   |
| PRECISION          | INTEGER       | 绑定变量的精度（数值类型有效）           |
| SCALE              | INTEGER       | 绑定变量的范围（数值类型有效）           |
| MAX_LENGTH        | INTEGER       | 绑定变量的最大长度           |
| WAS_CAPTURED      | VARCHAR(3)    | 表示绑定变量的值是否被捕获       |
| LAST_CAPTURED     | DATE          | 最近一次捕获绑定变量的时间（间隔时间受隐藏参数_CURSOR_BIND_CAPTURE_INTERVAL影响）     |
| VALUE_STRING      | VARCHAR(4000) | 绑定变量的值，使用字符串表示  |
