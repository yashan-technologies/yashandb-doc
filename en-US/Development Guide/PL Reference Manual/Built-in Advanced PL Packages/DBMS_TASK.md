The DBMS_TASK advanced package provides a set of built-in stored procedures for the *yasboot* tool to create and manage distributed tasks, currently only supporting scaling-related tasks.

> **Caution**:
>
> - It is not recommended for users to directly call the DBMS_TASK advanced package to avoid uncontrollable impacts.
>
> - The DBMS_TASK advanced package is only applicable for ISC Distributed Cluster Deployment.

## ADD

```plsql
DBMS_TASK.ADD(
    TASK_ID  OUT BIGINT,
    TYPE     IN VARCHAR(16),
    DATA     IN JSON,
)
```

This stored procedure is used to add a distributed task. Once created successfully, task-related information can be queried in the TASK$ system table.

|Parameter |Purpose |
| --- | --- |
| TASK_ID | The task ID automatically generated during task creation, globally unique. |
| TYPE | The type of task being created, optional values include 'ADD_GROUP', 'REMOVE_GROUP', 'SCALE_OUT', etc. |
| DATA | The parameters required for creating the task. |

## REMOVE

```plsql
DBMS_TASK.REMOVE(
    TASK_ID  IN BIGINT,
)
```

This stored procedure is used to delete a distributed task that is not in execution status. Successful deletion can be confirmed through the task information recorded in the task$ system table.

|Parameter |Purpose |
| --- | --- |
| TASK_ID | The ID of the target task. |

## START

```plsql
DBMS_TASK.START(
    TASK_ID  IN BIGINT,
)
```

This stored procedure is used to start a root task that is in INIT status. A subtask cannot be started directly. Once started, the task status will change to START.

|Parameter |Purpose |
| --- | --- |
| TASK_ID | The ID of the target task. |

## STOP

```plsql
DBMS_TASK.STOP(
    TASK_ID  IN BIGINT,
)
```

This stored procedure is used to stop a root task that is in START status. A subtask cannot be stopped directly.

|Parameter |Purpose |
| --- | --- |
| TASK_ID | The ID of the target task. |

## ROLLBACK

```plsql
DBMS_TASK.ROLLBACK(
    TASK_ID  IN BIGINT,
)
```

This stored procedure is used to roll back a root task that is in FAILED status. A subtask cannot be rolled back directly.

|Parameter |Purpose |
| --- | --- |
| TASK_ID | The ID of the target task. |