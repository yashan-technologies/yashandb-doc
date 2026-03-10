DBMS_TASK高级包提供了一组内置的存储过程，用于yasboot工具创建和管理分布式任务，目前仅支持扩缩容相关任务。

> **Caution**:
>
> - 不建议用户直接调用DBMS_TASK高级包，避免造成不可控影响。
>
> - DBMS_TASK高级包仅适用于存算一体分布式集群部署。

## ADD

```plsql
DBMS_TASK.ADD(
    TASK_ID  OUT BIGINT,
    TYPE     IN VARCHAR(16),
    DATA     IN JSON,
)
```

该存储过程用于新增一个分布式任务，创建成功后可以在TASK$系统表中查询任务相关信息。

|  参数| 用途|
| --- | --- |
| TASK_ID | 任务创建时自动生成的任务ID，全局唯一。 |
| TYPE | 创建的任务类型，可选值包括'ADD_GROUP'、'REMOVE_GROUP'，'SCALE_OUT'等。 |
| DATA | 创建任务所需参数。 |

## REMOVE

```plsql
DBMS_TASK.REMOVE(
    TASK_ID  IN BIGINT,
)
```

该存储过程用于删除一个非执行状态的分布式任务，可通过task$系统表记录的任务信息确认是否删除成功。

|  参数| 用途|
| --- | --- |
| TASK_ID | 目标任务的ID。 |

## START

```plsql
DBMS_TASK.START(
    TASK_ID  IN BIGINT,
)
```

该存储过程用于启动一个状态为INIT的根任务，不能直接启动某个子任务。启动后，任务状态将变为START。

|  参数| 用途|
| --- | --- |
| TASK_ID | 目标任务的ID。 |

## STOP

```plsql
DBMS_TASK.STOP(
    TASK_ID  IN BIGINT,
)
```

该存储过程用于停止一个状态为START的根任务，不能直接停止某个子任务。

|  参数| 用途|
| --- | --- |
| TASK_ID | 目标任务的ID。 |

## ROLLBACK

```plsql
DBMS_TASK.ROLLBACK(
    TASK_ID  IN BIGINT,
)
```

该存储过程用于回滚一个状态为FAILED的根任务，不能直接回滚某个子任务。

|  参数| 用途|
| --- | --- |
| TASK_ID | 目标任务的ID。 |
