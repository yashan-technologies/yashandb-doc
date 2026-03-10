本视图显示所有嵌套表的信息。

| 字段                          | 类型             | 说明  |
|-----------------------------|----------------| --- |
| OWNER                       | VARCHAR(64)    | 嵌套表所属的用户 |
| TABLE_NAME                 | VARCHAR(64)    | 该列所属对象名称 |
| TABLE_TYPE_OWNER          | VARCHAR(64)    | 嵌套表对应的nested table类型所属的用户名 |
| TABLE_TYPE_NAME           | VARCHAR(64)    | 嵌套表对应的nested table类型的名称 |
| PARENT_TABLE_NAME         | VARCHAR(64)    | 嵌套表的父表的名称 |
| PARENT_TABLE_COLUMN       | VARCHAR(4000)  | 嵌套表所对应的父表的列名 |
| STORAGE_SPEC               | VARCHAR(14)    | 嵌套表的存储选项是否由用户指定(USER_SPECIFIED/DEFAULT) |
| RETURN_TYPE                | VARCHAR(7)     | 嵌套表返回值类型，内部使用 |
| ELEMENT_SUBSTITUTABLE      | VARCHAR(1)     | 嵌套表的成员类型是否可替代 |