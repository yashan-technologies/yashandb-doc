```ebnf+diagram
current_timestamp::= CURRENT_TIMESTAMP  ["(" [integer] ")"] 
```
CURRENT_TIMESTAMP函数用于获取当前时间，即当前会话时间戳。

在YashanDB不同部署形态中，本函数表现不同：

- 在单机、共享集群部署中，返回值包含时区信息（会话时区），返回类型为TIMESTAMP WITH TIME ZONE。

    >**Caution**:
    >
    > TIMESTAMP WITH TIME ZONE是YashanDB 23.4新增的数据类型，成功执行本函数要求客户端（包括yasql、驱动接口等）高于23.4及23.4配套版本。

- 在分布式部署中，返回类型为TIMESTAMP。

**integer**

必须为一个0 ~ 9之间的整数字面量，表示保留的微秒位数，舍去的位按四舍五入。

示例（单机、共享集群部署）

```sql
SELECT CURRENT_TIMESTAMP() res FROM DUAL;
RES
----------------------------------------------------------------
2022-04-12 18:37:26.008000
```

示例（分布式部署）
```sql
SELECT CURRENT_TIMESTAMP() res FROM dual;
RES
----------------------------------------------------------------
2022-04-12 18:37:26.008000
```

