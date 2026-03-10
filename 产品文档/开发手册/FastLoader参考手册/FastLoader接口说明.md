## 功能相关接口

描述FastLoader功能的接口如下表所示，FastLoader有严格的执行顺序，数据导入完成后必须调用close()接口关闭FastLoader，且执行close()后不能再调用prepare，execute等接口。

| 类 | 返回类型 | 方法              | 参数 | 说明                                       |
|-----| -------- | ----------------- |---------------- |------------------------------------------ |
| FastLoaderFactory | FastLoader | generatefastLoader(String mode, String ipPort, Properties info) | \[1\] mode： 支持插入数据的模式，有BASIC和BATCH两个可选值，BASIC表示使用基础模式导入，BATCH表示使用批量模式导入。<br/>\[2\] ipPort： 要插入的数据库ipPort。<br/>\[3\] info： 创建connection相关的参数。| 创建对应模式的FastLoader。|
| FastLoader | void | prepare(String tableName) | \[1\] tableName： 要导入数据的表名。<br/> | 准备执行。|
| FastLoader | void | execute() | | 开始执行。开始执行以后，导入相关的后台线程开始运行。|
| FastLoader | boolean | putData(List<List\<Object>> data) | \[1\] data： 要导入的表数据。<br/> | 传入的数据，在fastLoad结束前可以多次调用，如果待发送行数达到最大待发送行数maxWaitLineCount的话，就返回false表示传入数据失败，需要等待一段时间再重新传入数据，传入成功返回true。|
| FastLoader | void | abort() | - | 强行中断此次导入。|
| FastLoader | void | finish() | - | 结束传入数据。finish以后不能继续进行putData传入数据操作。|
| FastLoader | void | close(long timeout, TimeUnit unit) | \[1\] timeout： 超时时间。<br/>\[2\] unit： 超时单位。 | 关闭FastLoader，并释放FastLoader资源。接口会阻塞式等待数据导入完成再结束释放资源。若未在超时时间范围内结束，则抛出InterruptedException异常。|

## 配置相关接口

描述FastLoader配置属性的接口如下表所示，如需调整FastLoader配置属性，建议在调用prepare()接口前完成。

| 类 | 返回类型 | 方法              | 参数 | 说明                                       |
|-----| -------- | ----------------- |---------------- |------------------------------------------ |
| FastLoader | void | setColumnNames(String columnNames) | \[1\] columnNames： 要插入表数据的列名称。支持插入部分列，不设置表示默认插入所有列数据。| 设置需要插入的列名称。|
| FastLoader | void | setReaderCount(int readerCount) | \[1\] readerCount： 执行读取解析数据的线程的个数。<br/> | 设置读取线程的个数，默认2个。|
| FastLoader | void | setSenderCount(int senderCount) | \[1\] senderCount： 执行发送数据的线程的个数。<br/> | 设置发送线程的个数，默认2个，建议设置为CPU的核数。|
| FastLoader | void | setSendCountAtOnce(int sendCountAtOnce) | \[1\] sendCountAtOnce： 每次发送数据的行数。<br/> | 设置Sender线程每次发送数据的行数，默认10000行。|
| FastLoader | void | setCommitCount(int commitCount) | \[1\] commitCount： 提交事务行数，值为0时表示设置bulkLoad属性，即中途不提交，只有LSC表生效。<br/> | 设置发送多少行提交一次事务，默认中途不提交执行插入完以后提交。|
| FastLoader | void | setMaxWaitLineCount(int maxWaitLineCount) | \[1\] maxWaitLineCount： 最大等待发送行数，值为0时表示不控制等待发送行数。<br/> | 设置最大等待发送行数，默认不控制等待发送行数。此参数用于控制在内存中的待发送数据大小。|
| FastLoader | void | setIsDeduplicated(boolean deduplicated) | \[1\] deduplicated： 是否允许重复数据。<br/> | 设置是否允许重复数据，默认为false。若设置为true，出现重复数据时会执行update操作。<br/>使用deduplicated属性（设置为true）必须符合如下要求，否则报错：<br/>* generatefastLoader()的mode为batch。<br/>* 待导入数据的表类型为LSC表。<br/>* setCommitCount()的commitCount为0。|

## 获取执行进度相关接口

描述FastLoader执行进度的接口如下表所示。

| 类 | 返回类型 | 方法              | 参数 | 说明                                       |
|-----| -------- | ----------------- |---------------- |------------------------------------------ |
| FastLoader | LoaderProgress | getProgress() | - | 获取执行进度的类。|
| LoaderProgress | int  | getTotalCount() | - | 获取全部数据行数。|
| LoaderProgress | int  | getErrorCount() | - | 获取执行错误数据行数。|
| LoaderProgress | int  | getInsertedCount() | - | 获取已插入数据行数。|
| LoaderProgress | double | getPercentage() | - | 获取执行进度百分比（0-100）。|
| LoaderProgress | StringBuffer | getErrorMsg() | - | 获取所有错误信息。|