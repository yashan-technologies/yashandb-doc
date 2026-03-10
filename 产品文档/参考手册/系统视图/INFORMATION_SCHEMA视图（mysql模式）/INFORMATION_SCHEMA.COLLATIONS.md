本视图显示可用排序规则信息。

| 字段                  | 类型          | 说明               |
|---------------------|-------------|------------------|
| COLLATION_NAME      | VARCHAR(32) | 排序规则名称           |
| CHARACTER_SET_NAME  | VARCHAR(32) | 排序规则所关联的字符集的名称   |
| ID                  | BIGINT(38)  | 排序规则ID           | 
| IS_DEFAULT          | VARCHAR(3)  | 排序规则是否是其字符集的默认设置 |
| IS_COMPILED         | VARCHAR(3)  | 是否已编译到服务器中       |
| SORTLEN             | BIGINT(38)  | 排序所需的内存量         |
 