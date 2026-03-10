DBMS_AWR包提供了一组内置的存储过程/函数，用于实现性能报告相关功能。

## CREATE\_SNAPSHOT

```plsql
DBMS_AWR.CREATE_SNAPSHOT ();
```

CREATE_SNAPSHOT子程序为存储过程，无参数，用于创建一个快照。

成功执行本程序后，系统将在WRM$_SNAPSHOT表中新增一条快照记录，该记录包含快照ID，快照时间等信息。

示例

```plsql
-- 创建一次快照
EXEC DBMS_AWR.CREATE_SNAPSHOT();
-- 继续创建一次快照
EXEC DBMS_AWR.CREATE_SNAPSHOT();

-- 从WRM$_SNAPSHOT表中查询最近的两次快照信息，包括保存快照信息的数据库ID、快照信息来源的数据库ID、快照ID、实例标识等信息
SELECT dbid,src_dbid,snap_id,instance_number,group_id,group_node_id FROM sys.wrm$_snapshot ORDER BY snap_id DESC LIMIT 2;

       DBID    SRC_DBID     SNAP_ID INSTANCE_NUMBER    GROUP_ID GROUP_NODE_ID 
----------- ----------- ----------- --------------- ----------- ------------- 
 3230486619  3230486619           3               1           0             0
 3230486619  3230486619           2               1           0             0
```

## AWR\_REPORT

```plsql
DBMS_AWR.AWR_REPORT(
	l_dbid     IN NUMBER,            
	l_inst_num IN VARCHAR2,           
  	l_bid      IN NUMBER,            
  	l_eid      IN NUMBER,           
  	l_options  IN NUMBER   DEFAULT 0,
  	l_format   IN VARCHAR2 DEFAULT 'html');
```

AWR_REPORT子程序为存储过程，用于生成性能报告。

本程序在成功执行后，将直接输出报告的文本内容，用户可将输出内容粘贴到一个html文件，用于查看和保存。

|  参数| 描述|
| :--------- | :----------------------------------------------------------- |
| l_dbid     | l_inst_num所在的数据库ID，对应WRM$_SNAPSHOT表中的SRC_DBID        |
| l_inst_num | 实例标识符<br/>* 在单机部署中，该参数值为1<br/>* 在共享集群/分布式集群部署中，该参数值取自GV$INSTANCE.INSTANCE_NUMBER<br/>* 在存算一体分布式集群部署中，该参数值取自GV$INSTANCE.GROUP_ID和GROUP_NODE_ID的拼接字符串，输入格式形如'3-1' |
| l_bid      | 起始快照ID                                                   |
| l_eid      | 终止快照ID                                                   |
| l_options  | 保留字段                                                     |
| l_format   | 保留字段                                                     |

示例

```plsql
-- 从WRM$_SNAPSHOT表获取两个snap_id值
-- 单机/共享集群/分布式集群部署
EXEC DBMS_AWR.AWR_REPORT(2621752453,1,168,169);

-- 存算一体分布式集群部署
EXEC DBMS_AWR.AWR_REPORT(2621752453,'3-1',168,169);
```

## CLEAN\_SNAPSHOT

```plsql
DBMS_AWR.CLEAN_SNAPSHOT ();
```

CLEAN_SNAPSHOT子程序为存储过程，无参数，用于检查之前生成的快照是否超过设置的保存时间（默认是8天），超过则进行清理。

示例（单机/共享集群/分布式集群部署）

```plsql
EXEC DBMS_AWR.CLEAN_SNAPSHOT();
```

## DROP\_SNAPSHOT\_RANGE

```plsql
DBMS_AWR.DROP_SNAPSHOT_RANGE (
	low_snap_id      IN NUMBER,         
	high_snap_id     IN NUMBER,         
	dbid             IN NUMBER DEFAULT NULL);
```

DROP_SNAPSHOT_RANGE子程序为存储过程，通过给定参数确定所需要删除快照的范围，删除相应区间的快照。

|  参数| 描述|
| :----------- |:---------------------------------------------------------|
| low_snap_id  | 快照起始ID                                                   |
| high_snap_id | 快照终止ID                                                   |
| dbid         | 保存快照信息的数据库ID，对应WRM$_SNAPSHOT表中的DBID；可省略，则自动填充当前的数据库ID |

示例

```plsql
EXEC DBMS_AWR.DROP_SNAPSHOT_RANGE(35, 40);
```

## MODIFY\_SNAPSHOT\_SETTINGS

```plsql
DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(
	retention       IN NUMBER   DEFAULT NULL,        
	interval        IN NUMBER   DEFAULT NULL,
    dbid            IN NUMBER   DEFAULT NULL,
    topnsql         IN VARCHAR2 DEFAULT NULL);
```

MODIFY_SNAPSHOT_SETTINGS子程序为存储过程，通过修改快照的相关属性，控制快照的生成时间间隔（用于生成快照的自动任务中）及保存时间。

|  参数| 描述|
| :-------- |:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| retention | 快照保存时间，单位分钟，如保存一天可输入：24*60（最小值一天，最大值100年）；设置为0，表示永久保留快照                                                                                                                                                      |
| interval  | 快照生成时间间隔，单位分钟，如20分钟生成一次快照，可输入：20（最小值10分钟，最大值100年）；设置为0，表示禁用手动和自动快照采集                                                                                                                            |
| dbid      | 保存快照信息的数据库ID，可省略，则自动填充当前的数据库ID                                                                                                                                                                             |
| topnsql   | 指定生成快照时每个SQL标准 (ELAPSED_TIME、CPU_TIME、PARSE_CALLS、SHARABLE_MEM) 刷新的Top SQL数量，默认为NULL，表示保持当前设置。允许用户指定为以下值：<br>* 具体数值N：即TOP N，取值范围为[30,50000]。<br>* DEFAULT：表示使系统恢复默认行为，即TOP 30。<br>* MAXIMUM：表示获取视图中的全部SQL。 |

> **Note**: 
>
> 系统默认快照保存时间为8天，默认快照生成时间间隔为1小时。

示例

```plsql
-- 将快照保存时间设为3天，将快照生成间隔时间设为2小时
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(3*24*60,2*60);

-- 将top sql数量指定为60
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(topnsql=>60);

-- 将top sql数量指定为默认值
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(topnsql=>'DEFAULT');

-- 将top sql数量指定为MAXIMUM
EXEC DBMS_AWR.MODIFY_SNAPSHOT_SETTINGS(topnsql=>'MAXIMUM');
```
