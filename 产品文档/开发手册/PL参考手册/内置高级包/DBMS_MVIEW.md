DBMS_MVIEW包提供了一组内置的存储过程，用于物化视图刷新相关操作。

分布式部署中无物化视图相关功能。

## REFRESH

```plsql
DBMS_MVIEW.REFRESH(
	tab IN VARCHAR,
    method IN VARCHAR,
    refresh_after_errors IN BOOLEAN DEFAULT FALSE,
    purge_option IN	INTEGER DEFAULT 1,
    parallelism IN INTEGER DEFAULT 0,
    atomic_refresh IN BOOLEAN DEFAULT TRUE,
    nested IN BOOLEAN DEFAULT FALSE,
    out_of_place IN BOOLEAN DEFAULT FALSE);
```

REFRESH程序用于指定一个物化视图手动刷新数据。

| 参数                 | 描述                                                         |
| :------------------- | :----------------------------------------------------------- |
| tab                  | 物化视图名字。                                               |
| method               | 物化视图刷新模式，默认为C（全量刷新），可省略。 |
| refresh_after_errors | 兼容参数，仅列表兼容。                                       |
| purge_option         | 兼容参数，仅列表兼容。                                       |
| parallelism          | 并行度兼容参数，仅列表兼容。                                 |
| atomic_refresh       | 指定TRUE时，刷新行为是delete全表+insert into select；指定为FALSE时，刷新行为是truncate全表+insert into select |
| nested               | 嵌套刷新，兼容参数，仅列表兼容。                             |
| out_of_place         | 兼容参数，仅列表兼容。                                       |

示例（HEAP表）

```plsql
-- 创建物化视图并指定为手动刷新模式
CREATE MATERIALIZED VIEW TEST_MVIEW 
REFRESH ON DEMAND
AS SELECT * FROM area;

-- 执行高级包刷新物化视图数据
EXEC DBMS_MVIEW.REFRESH('TEST_MVIEW');
```