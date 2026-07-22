本视图显示各种操作的会话累积时间。

|  字段| 类型| 说明|
|-------------|-------------|-------|
| SID         | SMALLINT    | 会话ID  |
| STAT\_ID    | INTEGER     | 统计项ID |
| STAT\_NAME  | VARCHAR(64) | 统计项名称<br>\*   DB TIME：执行数据库用户级调用所花费的时间（单位：微秒）<br>\*   PARSE TIME ELAPSED：解析SQL语句所花费的总时间（单位：微秒）<br>\*   HARD PARSE ELAPSED TIME：硬解析SQL语句所花费的时间（单位：微秒）<br>\*   SQL EXECUTE ELAPSED TIME：执行SQL语句所花费的时间（单位：微秒） |
| VALUE       | BIGINT      | 统计值   |
