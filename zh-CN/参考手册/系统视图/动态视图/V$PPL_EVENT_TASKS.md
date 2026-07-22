执行流水线并行执行的任务视图，显示当前执行流水线中各任务的执行状态。

|  字段| 类型| 说明|
| --- | --- | --- |
|  SID | SMALLINT | 会话ID |
| GLOBAL_SESSION_ID | INTEGER | 全局会话ID |
| SQL_ID  | VARCHAR(13) | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得 |
| HASH_VALUE | BIGINT |  一个执行计划的唯一标识  |
| PPL_ID |  SMALLINT  | 在pipeline并行执行模型下，一个执行计划内的pipeline标识  |
| EVENT_ID | SMALLINT | 执行流水线的event ID |
| EVENT_TYPE | SMALLINT | event的类型：<br/>* 0：VEC_EVENT_PPL_INIT，init事件  <br/>* 1：VEC_EVENT_PPL_PUSH，push事件  <br/>* 2：VEC_EVENT_PPL_ONFINISH，onfinish事件  <br/>* 3：VEC_EVENT_PPL_FINISH，finish事件  <br/>* 4：VEC_EVENT_PPL_COMPLETE，complete事件  <br/>* 5：VEC_EVENT_HJ_REPARTITION，repartition事件  <br/>* 6：VEC_EVENT_HJ_TABLE_BUILD，hash表build事件  <br/>* 7：VEC_EVENT_HJ_RUNTIME，runtime filter build事件  <br/>* 8：VEC_EVENT_SORT_MERGE，sort merge事件  <br/>* 9：VEC_EVENT_WINDOW_PART_MERGE，window part merge事件  <br/>* 10：VEC_EVENT_INSERT，insert事件 |
| EVENT_CREATE | TIMESTAMP(6) | event的创建时间 |
| EVENT_FINISH | TIMESTAMP(6) | event的完成时间 |
| EVENT_ISRESCHEDULE | SMALLINT | event是否重调度 |
| TASK_ID | SMALLINT | 任务ID |
| TASK_STATUS | INTEGER | 当前task的状态：<br/>* 0：TASK_STATUS_INIT，任务初始化 <br/>* 1：TASK_STATUS_SCHEDULED，任务就绪 <br/>* 2：TASK_STATUS_DESCHEDULED，任务未就绪 <br/>* 3：TASK_STATUS_READY，任务准备执行<br/>* 4：TASK_STATUS_RUNNING，任务正在执行 <br/>* 5：TASK_STATUS_IDLE，任务处于空闲 <br/>* 6：TASK_STATUS_BLOCKED，任务被阻塞 <br/>* 7：TASK_STATUS_FINISHED，任务执行完成 <br/>* 8：TASK_STATUS_ERROR，任务执行出错 <br/>* 9：TASK_STATUS_DESTROYED，任务已销毁 |
| SCH_COUNT | INTEGER | 当前task被调度的次数 |
| DESCH_COUNT | INTEGER | task被阻塞的次数  |
| LAST_BLOCK_POS | VARCHAR(50) | 当前task最后一次被阻塞的位置，格式为：<br/>NOBLOCK<br/>MSTBUILD<br/>PXSINK<br/>PXCOMBINE<br/>PXSOURCE<br/>HJSOURCE<br/>WINSOURCE<br/>HJNASYNC  |
| TASK_CREATE | TIMESTAMP(6) | task的创建时间 |
| TASK_FINISH | TIMESTAMP(6) | task的完成时间 |
 |
