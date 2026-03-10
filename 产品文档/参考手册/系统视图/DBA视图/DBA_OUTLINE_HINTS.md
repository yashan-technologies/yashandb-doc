本视图显示所有OUTLINE对应的hint信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| NAME | VARCHAR(64) | 对象名称 |
| OWNER | VARCHAR(64) | 对象所属用户名 |
| NODE | SMALLINT | 应用提示的查询或子查询的ID，顶级查询标识为1 |
| STAGE | SMALLINT | 应用hint的阶段 |
| JOIN_POS | SMALLINT | 表在联接顺序中的位置 |
| HINT | CLOB | hint内容 |