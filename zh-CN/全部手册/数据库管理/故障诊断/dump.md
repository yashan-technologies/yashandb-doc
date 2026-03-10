YashanDB提供了多种捕获诊断数据的方法，助力于故障问题跟踪与分析。

## 手动捕获诊断数据

YashanDB提供dump语句，用户可按需获取系统内部结构信息。

> **Note**: 
>
> 如在故障跟踪时希望获得相关内部结构数据的详细解析，请咨询我们的技术支持。

### dump语句

dump基于SQL语句实现，用于转储执行操作时刻的内部数据（多次dump同类信息便可跟踪变化），详细的语法描述请查阅[ALTER SYSTEM](../../开发手册/SQL参考手册/SQL语句（yashan模式）/ALTER SYSTEM)的dump_clause子句。

dump操作涉及磁盘写，运行时会对IO产生一定影响，请合理安排操作时间。

YashanDB允许将如下内部信息dump到trace文件中：

- **private redo in memory**：内存中的私有日志数据

    ```sql
    ALTER SYSTEM DUMP PRIVATE LOG;
    ```

- **logfile blocks**：磁盘中的redo日志数据

    ```sql
    ALTER SYSTEM DUMP LOGFILE 'redo1'; 
    ```

- **datafile blocks**：磁盘中的数据文件数据

    ```sql
    -- dump整个数据文件
    ALTER SYSTEM DUMP DATAFILE 6;

    -- dump一个页面
    ALTER SYSTEM DUMP DATAFILE 6 BLOCK 0;

    -- dump一批页面
    ALTER SYSTEM DUMP DATAFILE 6 MINBLOCK 128 MAXBLOCK 137;
    ```

- **stack of this session**：某个会话的堆栈数据

    ```sql
    ALTER SYSTEM DUMP SESSION 20 BACKTRACE;
    ```

- 死锁信息（自动触发dump）

    死锁信息无法手动通过SQL语句转储，只能在检测到死锁时自动触发。当数据库后台线程检测到出现死锁时，会选择死锁环中的某一个会话返回相应报错，同时自动dump对应的[死锁信息](#deadlock)到该会话对应的trace文件中。

### dump结果

首次执行dump操作后将生成与会话ID（sid）一一对应的trace文件，默认文件名格式为{dbname}_yas_{sid}.trc，后续的每一次dump操作则只会在同一个会话ID对应的已有文件中追加写入新的一段数据。

> **Note**: 
>
> 由于会话ID允许重复使用，当相同ID的新会话第一次dump且该ID对应trace文件已存在时，不会创建新文件，只会继续追加写入。

每一次dump操作对应文件中的一段内容，每一段内容由标题和数据组成：

- 标题：包含dump时间、dump类型、dump数据大小等信息。

- 数据：根据不同的dump类型，获取到的系统内部结构数据。

<span id="blackbox" name="blackbox" class="yaslink"></span>

## 自动捕获诊断数据

YashanDB提供自动捕获诊断数据机制，在数据库进程出现故障宕机前，收集进程运行堆栈等信息，将诊断数据dump到自动诊断存储库的blackbox目录中。该机制类似于飞行记录仪（即飞机“黑匣子”），故又称 “黑匣子” 。

> **Note**: 
>
> 当收集到 “黑匣子” 诊断数据，请打包诊断数据并及时联系我们的技术支持。

<span id="deadlock" name="deadlock" class="yaslink"></span>

## 死锁检测诊断数据

YashanDB提供死锁自动检测机制，后台线程SMON会定时检测是否发生死锁，若检测到死锁会自动将死锁环信息dump到对应的trace文件中。

死锁环信息包括资源标识、资源持有者、资源请求者、死锁会话ID、会话用户以及会话执行的SQL语句等。

资源标识分为：

- `TM-XXXX-XXXX`：表锁资源，XXXX-XXXX为object id，即表对象ID。

- `TX-XXXX-XXXX`：事务锁与xslot锁资源，XXXX-XXXX为xext（前两位16进制数）+xnode（中间两位16进制数）+xsn（后四位16进制数）。

锁类型分为：

- X：表示排它锁。

- S：表示共享锁。

```shell
Deadlock graph:
                                         -----------Blocker(s)------------ -----------Waiter(s)-------------
 Resource Name                           instId session holds waits serial instId session holds waits serial
 TX-0031344A-00000001                         0      25     X            3      0      22           X      4
 TX-0030344A-00000001                         0      22     X            4      0      25           X      3
 ----- Information for waiting sessions -----
 instId 0 Session 22:
   sid: 22 serial: 4 user: SYS
   current SQL:
   UPDATE t1 SET a = 3 WHERE b = 4
 instId 0 SESSION 25:
   sid: 25 serial: 3 USER: SYS
   CURRENT SQL:
   UPDATE t1 SET a = 1 WHERE b = 2
 ----- END OF information FOR waiting sessions -----
```
