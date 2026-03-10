## Interfaces Related to Functionality

The interfaces for describing FastLoader functionality are shown in the table below. FastLoader has a strict execution order. After data import is completed, the close() interface must be called to close FastLoader, and after executing close(), prepare, execute, and other interfaces cannot be called again.

|Class |Return Type |Method |Parameters |Description |
|-----| -------- | ----------------- |---------------- |------------------------------------------ |
| FastLoaderFactory | FastLoader       | generatefastLoader(String mode, String ipPort, Properties info) | \[1\] mode: The mode for inserting data, with two optional values, BASIC and BATCH. BASIC indicates using the basic mode for import, BATCH indicates using the batch mode for import.<br/>\[2\] ipPort: The ipPort of the database for inserting data.<br/>\[3\] info: Parameters related to creating the connection. | Creates a FastLoader of the corresponding mode.                                             |
| FastLoader        | void             | prepare(String tableName)                  | \[1\] tableName: The name of the table where the data will be imported.<br/> | Prepares for execution.                                                                          |
| FastLoader        | void             | execute()                                  |            | Starts execution. After execution starts, the background threads related to import begin to run. |
| FastLoader        | boolean          | putData(List<List\<Object>> data)          | \[1\] data: The table data to be imported.<br/> | Input data can be called multiple times before fastLoad ends. If the number of rows to be sent reaches the maximum wait line count maxWaitLineCount, it returns false to indicate that the input data failed, and it needs to wait for a while before re-inputting data. If input is successful, it returns true. |
| FastLoader        | void             | abort()                                    | -          | Forcefully interrupts the current import.                                                      |
| FastLoader        | void             | finish()                                   | -          | Ends data input. After finish, no more putData operations can be carried out.                  |
| FastLoader        | void             | close(long timeout, TimeUnit unit)        | \[1\] timeout: Timeout duration.<br/>\[2\] unit: Timeout unit. | Closes FastLoader and releases FastLoader resources. The interface will block and wait for data import completion before releasing resources. If not completed within the timeout, an InterruptedException will be thrown. |

## Interfaces Related to Configuration

The interfaces for describing FastLoader configuration properties are shown in the table below. It is recommended to complete adjustments to FastLoader configuration properties before calling the prepare() interface.

|Class |Return Type |Method |Parameters |Description |
|-----| -------- | ----------------- |---------------- |------------------------------------------ |
| FastLoader        | void             | setColumnNames(String columnNames)        | \[1\] columnNames: The names of the columns for inserting table data. Supports partial column insertion. Not setting indicates that all column data will be inserted by default. | Sets the column names to be inserted.                                                           |
| FastLoader        | void             | setReaderCount(int readerCount)           | \[1\] readerCount: The number of threads executing data reading and parsing.<br/> | Sets the number of reading threads, default is 2.                                               |
| FastLoader        | void             | setSenderCount(int senderCount)           | \[1\] senderCount: The number of threads executing data sending.<br/> | Sets the number of sending threads, default is 2, recommended to set it to the number of CPU cores. |
| FastLoader        | void             | setSendCountAtOnce(int sendCountAtOnce)  | \[1\] sendCountAtOnce: The number of rows sent each time.<br/> | Sets the number of rows sent by the Sender thread at once, default is 10,000 rows.             |
| FastLoader        | void             | setCommitCount(int commitCount)           | \[1\] commitCount: The number of rows to commit a transaction. A value of 0 indicates setting the bulkLoad property, which means no intermediate commits, only effective for LSC tables.<br/> | Sets how many rows to commit at once, default is no intermediate commits, commit after insertion completes. |
| FastLoader        | void             | setMaxWaitLineCount(int maxWaitLineCount) | \[1\] maxWaitLineCount: The maximum number of rows waiting to be sent. A value of 0 means no control over the number of rows waiting to be sent.<br/> | Sets the maximum number of rows waiting to be sent, default does not control waiting sent rows. This parameter is used to control the size of unsent data in memory. |
| FastLoader        | void             | setIsDeduplicated(boolean deduplicated)   | \[1\] deduplicated: Whether to allow duplicate data.<br/> | Sets whether to allow duplicate data, default is false. If set to true, an update operation will be executed when duplicate data appears.<br/> Using the deduplicated property (set to true) must meet the following requirements, otherwise an error will be reported:<br/>* The mode of generatefastLoader() is batch.<br/>* The type of table to be imported is LSC table.<br/>* The commitCount of setCommitCount() is 0. |

## Interfaces Related to Execution Progress

The interfaces for describing the execution progress of FastLoader are shown in the table below.

|Class |Return Type |Method |Parameters |Description |
|-----| -------- | ----------------- |---------------- |------------------------------------------ |
| FastLoader        | LoaderProgress    | getProgress()                              | -          | Gets the class representing execution progress.                                                 |
| LoaderProgress    | int              | getTotalCount()                            | -          | Gets the total number of data rows.                                                              |
| LoaderProgress    | int              | getErrorCount()                            | -          | Gets the number of data rows that encountered execution errors.                                  |
| LoaderProgress    | int              | getInsertedCount()                         | -          | Gets the number of data rows that have been inserted.                                           |
| LoaderProgress    | double           | getPercentage()                            | -          | Gets the execution progress percentage (0-100).                                                |
| LoaderProgress    | StringBuffer     | getErrorMsg()                             | -          | Gets all error messages.                                                                         |