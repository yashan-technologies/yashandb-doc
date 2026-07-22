在单机部署一主三备（非级联备）中，已实现yasom的高可用，即yasom自选主功能。

## 功能简介

开启yasom自选主功能后，备yasom会自动检测主yasom是否正常，一旦发现其故障将触发选主，无需人工介入实现yasom的角色切换。

yasom自选主功能需要依赖YCM才能实现和正常运转：

- 异地存储关键元数据：使用YCM的后台数据库存储记录主备yasom心跳和自选主相关信息的心跳表。

- 故障隔离：目标数据库故障与yasom自选主元数据故障完全隔离。

### 心跳检测与选主

所有主备yasom共享一张心跳表（名为`om_heartbeat`），表中仅保存当前主yasom的心跳记录，包含任期号（`term`）和心跳计数（`heartbeat_cnt`）。

心跳表字段介绍如下：

| 字段名            | 说明                          |
|----------------|-----------------------------|
| om_uuid        | 主备yasom的UUID                |
| om_addr        | 主yasom的监听地址                 |
| agent_addr     | 主yasom机器上的yasagent监听地址      |
| hostid         | 主yasom所在服务器的ID              |
| id             | 主键                          |
| term           | 任期                          |
| heartbeat_cnt  | 主yasom的心跳次数                 |
| om_data        | yasom的元数据，用于yasom主备切换后的数据同步 |
| last_heartbeat | 主yasom的最近一次心跳时间             |

- **备yasom检测心跳**：备yasom按心跳间隔（默认1秒）查询心跳表中的`heartbeat_cnt`。若计数持续增长，说明主yasom正常；若计数在超时时间（默认5秒）内未变化，则判定主yasom故障，触发选主。

- **选主竞争**：多个备yasom同时发起选主时，各自读取当前的`term`和`heartbeat_cnt`，并尝试执行带有条件判断的原子更新。由于数据库保证同一时刻只有一个更新能匹配条件并生效，因此只有一个备yasom能成功升主。成功后会将`term`加1，并更新`om_uuid`、`listenAddr`字段为主yasom信息。

- **元数据同步**：主yasom定时将本地sqlite元数据导出为SQL，写入心跳表的 `om_data` 字段，新主选主后从该字段恢复元数据。

### 主yasom保活与自杀

- **保活**：主yasom按心跳间隔定时更新心跳表，将`heartbeat_cnt`加1，表明自身存活。

- **自杀**：若主yasom更新心跳时发现`term`已被其他实例修改（说明已有新主），或更新超时失败，则主动退出进程。退出后由系统进程监控工具（如monit）自动将其重启，重启后自动以备角色重新加入并恢复心跳检测。

### 状态转换

整个自选主过程的状态转换如下：

```text
Standby（备yasom） --选举成功--> Active（主yasom） --保活失败/任期变化--> Suicide（自杀） --> 重启 --> Standby（备yasom）
```

## 规格和约束

| 约束项    | 说明                                                                                                                                                                                                |
|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 数据库版本  | v23.4.14.100及以上                                                                                                                                                                                   |
| 部署形态   | 单机部署一主三备（非级联备）                                                                                                                                                                                    |
| YCM依赖  | 必须同时部署用于管理目标YashanDB的监控运维平台（YCM），且要求YCM为一主两备及以上。YCM正常运行期间，才能正常使用yasom自选主功能。YCM后台YashanDB数据库需要创建一个连接用户，并授予`create session`，`create table`，`create sequence`权限。                                     |
| 配置要求   | \* 心跳表必须创建在YCM后台YashanDB数据库中<br/> \* YCM后台YashanDB数据库必须开启自动选主（HA_ELECTION_ENABLED = TRUE）并采用最大保护模式<br/> \* 主备yasom进程必须与yasom管理的目标YashanDB部署在不同服务器上<br/> \* 开启yasom自选主后，不允许修改YCM后台YashanDB数据库的连接参数 |
| 配置变更约束 | 修改yasom自选主的心跳间隔和超时时间，需要关闭yasom自选主参数，修改后立即生效                                                                                                                                                       |
| 进程恢复机制 | 主yasom自杀后依赖yasboot进程监控工具（monit）自动拉起，也可以在yasom所在节点上执行`yasboot process yasom start -c yashandb`命令手动拉起                                                                                               |
| 功能互斥   | \* 与自修复（yasboot process yasom repair）互斥，不能同时开启 <br/> \* 与yasom恢复互斥，开启自选主后无法执行yasboot process yasom recover命令                                                                                      |
| 版本兼容   | 如果数据库进行过升级且升级前已部署备yasom，但从未使用过yasom高可用功能，升级后需要先执行`yasboot process yasom clean -c yashandb`清理备yasom后再重建备yasom。                                                                                     |

## 配置并使用yasom自选主

### 前提条件

- 至少准备7台服务器，4台用于部署单机一主三备业务数据库，另外3台用于运行yasom进程（可与YCM服务器重复）。

- 已完成YashanDB数据库部署前的[准备工作](../../../安装和升级/安装部署/安装前准备/00安装前准备.md)。

- 已完成YCM的部署，且与业务数据库网络互通。

- 已获取YCM后台数据库的连接信息。

### 操作步骤

1. 部署业务数据库：

   ```shell
   $ yasboot package se gen --cluster yashandb -u yashan -p yashan_password\
    --ip 192.168.1.1,192.168.1.2,192.168.1.3,192.168.1.4,192.168.1.5,192.168.1.6,192.168.1.7\
    --port 22  --install-path /data/yashan/yasdb_home --begin-port 1688 --node 4
   
   # 修改hosts.toml中yasom的hostid为host0005
   # [om]
   #   hostid = "host0005"
   
   # 修改yashandb.toml，删除host0005，host0006，host0007信息，
   ```
   ```shell
   $ yasboot package install -t hosts.toml
   
   $ yasboot cluster deploy -t yashandb.toml -p sys_password
   ```

2. 分别登录规划的其他yasom设备192.168.1.6、192.168.1.7并执行以下命令部署备yasom：

   ```shell
   $ yasboot process yasom recover -c yashandb -l 192.168.1.6:2775
   ```

   ```shell
   $ yasboot process yasom recover -c yashandb -l 192.168.1.7:2775
   
   # 查看yasom主备信息，获取备yasom的ip和端口
   $ yasboot process yasom status -c yashandb
   ```
   
3. 登录主yasom所在设备192.168.1.5并执行以下命令启动monit进程：

   ```shell
   $ yasboot monit start -c yashandb
   ```
   
4. 准备YCM后台数据库：
   
   ```shell
   # 设置最大保护模式
   $ yasql sys/********@192.168.2.1:1601 -c "alter database set standby database to MAXIMIZE PROTECTION;"
   
   # 开启自动选主
   $ yasboot cluster config set --key HA_ELECTION_ENABLED --value TRUE -c yashandb

   # 使用dba用户登录YCM后台数据库执行：
   SQL> create user yashan identified by yashan123;
   
   SQL> grant create table to yashan;   
   
   SQL> grant create session to yashan;
   
   SQL> grant create sequence to yashan;
   ```
   
5. 登录主yasom所在设备192.168.1.5并执行以下命令配置yasom高可用参数：

   ```shell
   $ yasboot process yasom config set -c yashandb \
     --heartbeat-interval 1 \
     --heartbeat-timeout 5 \
     --heartbeat-db-url 192.168.2.1:16789,192.168.2.2:16789,192.168.2.3:16789 \
     --heartbeat-db-user yashan \
     --heartbeat-db-password yashan123
   # 其中heartbeat-db-url对应YCM后台数据库的连接信息
   
   Connecting to yasom...
     Updating yasom election config on all hosts...
   
   Election config update completed
     Updated config on  (192.168.1.5:1675)
     Updated config on  (192.168.1.6:2775)
     Updated config on  (192.168.1.7:2775)
   
   All hosts updated successfully
   ```

6. 开启yasom自选主：

   ```shell
   $ yasboot process yasom election on -c yashandb
   
   Connecting to master yasom...
   Enabling election on all yasom...
   
   Election enable result:
     host0005 (192.168.1.5:1675)
     host0006 (192.168.1.6:2775)
     host0007 (192.168.1.7:2775)
   
   Election enabled successfully on all yasom
   ```

7. （可选）查询自选主配置：

   ```shell
   $ yasboot process yasom config show -c yashandb
   
   === Election Configuration ===
   
   Host: host0005 (192.168.1.5)
     Om Addr: 192.168.1.5:1675
     Role: primary
     Enabled: true
     Heartbeat Interval: 1 seconds
     Heartbeat Timeout: 5 seconds
     DB URL: 192.168.2.1:16789
     DB User: yashan
   
   Host: host0006 (192.168.1.6)
     Om Addr: 192.168.1.6:2775
     Role: secondary
     Enabled: true
     Heartbeat Interval: 1 seconds
     Heartbeat Timeout: 5 seconds
     DB URL: 192.168.2.1:16789
     DB User: yashan
   
   Host: host0007 (192.168.1.7)
     Om Addr: 192.168.1.7:2775
     Role: secondary
     Enabled: true
     Heartbeat Interval: 1 seconds
     Heartbeat Timeout: 5 seconds
     DB URL: 192.168.2.1:16789
     DB User: yashan
   ```

## 关闭yasom自选主

若需关闭yasom自选主功能，登录主yasom所在设备执行以下命令：

```shell
$ yasboot process yasom election off -c yashandb

Connecting to master yasom...
Disabling election on all yasom...

Election disable result:
  host0005 (192.168.1.5:2775)
  host0006 (192.168.1.6:1675)
  host0007 (192.168.1.7:1675)

Election disabled successfully on all yasom
```