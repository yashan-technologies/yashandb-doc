```ebnf+diagram
current_date::= CURRENT_DATE
```
CURRENT_DATE用于获取会话时区中的当前日期，返回值为DATE数据类型的公历值。

CURRENT_DATE返回值会因时区配置（[TIME_ZONE](../../../参考手册/配置参数.html#time_zone)参数）不同而存在差异。

示例（单机/共享集群/分布式集群部署）

```sql
ALTER SESSION SET DATE_FORMAT='yyyy-mm-dd hh24:mi:ss';

SHOW PARAMETER TIME_ZONE

NAME                                   VALUE
-------------------------------------- -----------------------------------------
TIME_ZONE                              +08:00

SELECT CURRENT_DATE,SYSDATE,SESSIONTIMEZONE FROM DUAL;

CURRENT_DATE                                     SYSDATE                                          SESSIONTIMEZONE
------------------------------------------------ ------------------------------------------------ ----------------------------------------------------------------
2025-08-14 15:16:24                              2025-08-14 15:16:24                              +08:00

-- 调整时区
ALTER SESSION SET TIME_ZONE = '-07:00';

-- 再次查询CURRENT_DATE
SELECT CURRENT_DATE,SYSDATE,SESSIONTIMEZONE FROM DUAL;

CURRENT_DATE                                     SYSDATE                                          SESSIONTIMEZONE
------------------------------------------------ ------------------------------------------------ ----------------------------------------------------------------
2025-08-14 00:16:38                              2025-08-14 15:16:38                              -07:00
```