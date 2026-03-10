DBMS_LOGSTDBY包主要用于管理逻辑备库。



逻辑复制相关功能适用于单机部署和共享集群部署，但在共享集群部署中仅用于滚动升级场景。


## BUILD

```plsql
DBMS_LOGSTDBY.BUILD ();
```

在主库上使用此存储过程会在redo日志中记录逻辑信息，并且自动开启主库的[库级附加日志](../../全部手册/开发手册/SQL参考手册/SQL语句/ALTER DATABASE.html#supplementallogclauses)，附加日志的模式为PRIMARY KEY，生效的表类型为HEAP。

将物理备库转换成逻辑备库前，必须在主库上执行该过程。

> **Note**: 
>
> 每创建一个逻辑备库，应运行一次DBMS_LOGSTDBY.BUILD。

示例（单机、共享集群部署）

```plsql
EXECUTE DBMS_LOGSTDBY.BUILD;
```

<span id="apply_set" name="apply_set" class="yaslink"></span>

## APPLY\_SET

APPLY_SET函数可以在逻辑备库中配置和管理SQL回放的参数值，其中APPLY_SERVERS参数无法在SQL回放期间更改。

```plsql
DBMS_LOGSTDBY.APPLY_SET (
     inname             IN VARCHAR,
     value              IN VARCHAR);
```

|  参数| 说明|
|-------|------|
| APPLY_SERVERS | 控制用于回放的线程数量，默认16，最小1，最大1024。|
| MAX_EVENTS_RECORDED | 通YashanDB会记录SQL回放遇到的所有事件，并在DBA_LOGSTDBY_EVENTS视图中呈现最近的N条事件（N由MAX_EVENTS_RECORDED参数值控制）。默认值为10000，最小值为1，最大值为1000000。 |

示例（单机、共享集群部署）

```plsql
--设置SQL回放的线程数为16
EXECUTE DBMS_LOGSTDBY.APPLY_SET('APPLY_SERVERS', '16');
```
