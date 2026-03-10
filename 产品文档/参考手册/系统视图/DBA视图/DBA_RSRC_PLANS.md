本视图显示数据库中所有资源计划信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| PLAN_ID                   | BIGINT        | 资源计划ID |
| PLAN                      | VARCHAR(64)   | 资源计划名称 |
| NUM_PLAN_DIRECTIVES       | BIGINT        | 资源计划包含的指令数量 |
| CPU_METHOD                | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| MGMT_METHOD               | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| ACTIVE_SESS_POOL_MTH      | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| PARALLEL_DEGREE_LIMIT_MTH | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| QUEUING_MTH               | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| SUB_PLAN                  | VARCHAR(3)    | 资源计划是否为子计划：是（YES）或不是（NO） |
| COMMENTS                  | VARCHAR(2000) | 资源计划的注释信息 |
| STATUS                    | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
| MANDATORY                 | VARCHAR(1)    | 仅用于兼容，目前值固定为`NULL` |
