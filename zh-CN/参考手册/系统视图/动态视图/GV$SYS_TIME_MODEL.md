本视图显示系统各种操作的累积时间。

| 字段          | 类型       |  说明   |
|-------------|-------------|-------|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID    | NUMBER      | 实例ID |
| STAT\_ID    | INTEGER     | 统计项ID |
| STAT\_NAME  | VARCHAR(64) | 统计项名称<br>\*   DB TIME：执行数据库用户级调用所花费的时间（单位：微秒）<br>\*   connection management call elapsed time：执行会话连接和断开连接调用所花费的时间（单位：微秒）<br>\*   PARSE TIME ELAPSED：解析SQL语句所花费的总时间（单位：微秒）<br>\*   HARD PARSE ELAPSED TIME：硬解析SQL语句所花费的时间（单位：微秒）<br>\*   SQL EXECUTE ELAPSED TIME：执行SQL语句所花费的时间（单位：微秒）<br>\*   PL/SQL execution elapsed time：执行PL所花费的时间（单位：微秒） <br>\*   PL/SQL compilation elapsed time：编译PL所花费的时间（单位：微秒） <br>\*   repeated bind elapsed time：为绑定变量赋予新值（重新绑定）所花费的时间（单位：微秒） <br>\*  Tablespace encryption elapsed time：执行表空间的加密和解密所花费的时间（单位：微秒） |
| VALUE       | BIGINT      | 统计值   |