本视图显示数据库支持的字符序信息。

| 字段        | 类型          | 描述       |
|-----------|-------------|----------|
| COLLATION | VARCHAR(32) | 字符序名     |
| CHARSET   | VARCHAR(32) | 对应字符集名   |
| ID        | BIGINT      | 字符序ID     |
| DEFAULT   | VARCHAR(3)  | 是否为默认字符序 |
| COMPILED  | VARCHAR(3)  | 是否已加载    |
| SORTLEN   | BIGINT      | 排序所需字节长度 |
| YAS_COLLATION_ID | INTEGER |  MySQL字符序对应的YashanDB字符序ID |
