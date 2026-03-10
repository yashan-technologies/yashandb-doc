YCS告警日志位于YCS的HOME目录下的log/alert文件夹中，名称为alert.log，路径及名称不可修改。

告警日志不设开关控制，恒为打开状态，即一直保持记录文件。记录文件大小没有设置上限，当alert.log文件过大时需要手动清理告警日志。

```verilog
$ cat $YASCS_HOME/log/alert/alert.log
2023-07-19 09:49:47.936|0|AlertReset|0|0||reset all reported alert
2023-07-19 09:52:16.096|0|AlertReset|0|0||reset all reported alert
2023-07-19 10:08:20.179|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-07-19 10:08:21.363|0|DbInstanceStopped|1|1|
2023-07-19 10:09:08.206|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-07-19 10:09:09.358|0|DbInstanceStopped|1|1|
```

## 服务器链接异常断开

inter channel closed：当结点之间的链路异常关闭时，记录告警日志，当链路重新恢复后记录消除告警的日志。

示例

```verilog
//在2023-06-13 11:20:15.391 结点1与本结点异常断开，触发InterChannelClosed告警事件
//第一个1表示结点编号，紧跟着1后面的0表示产生告警
//在2023-06-13 11:20:50.366 结点1重新连上与本结点连接上，消除该告警
//最后一个1表示消除告警
2023-06-13 11:20:15.391|0|InterChannelClosed|1|0||node id: 1 had disconnected
2023-06-13 11:20:50.366|0|InterChannelClosed|1|1|
```

## 访问投票盘异常

disk error：当访问投票盘异常时告警。当系统访问投票盘恢复正常时，消除告警。

访问投票盘异常包括：读盘异常、写盘异常。

示例

```verilog
2023-06-13 11:39:01.858|0|DiskError|0|0||voting file can't access
2023-06-13 11:39:01.858|0|DiskError|0|1|
```

## 数据库异常停止

db instance stopped：当发现YashanDB资源停止时，记录告警日志。重新连接上后记录消除告警的日志。

示例

```verilog
//在2023-06-13 11:48:37.593 发现资源编号为1的数据库已经停止，触发DbInstanceStopped告警事件
//紧跟DbInstanceStopped后面的1表示资源编号
//在2023-06-13 11:48:38.747 发现资源编号为1的数据库重新边上Ycs，消除该告警
2023-06-13 11:48:37.593|0|DbInstanceStopped|1|0||yasdb had stopped, that resource id is: 1
2023-06-13 11:48:38.747|0|DbInstanceStopped|1|1|
```
