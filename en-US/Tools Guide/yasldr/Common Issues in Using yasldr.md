#### Why does it show `[YASLDR] execute finished with warnings` instead of `[YASLDR] execute succeeded` after the import ends? 

The *yasldr* tool has the fault tolerance feature enabled by default to skip erroneous data and continue the import. If it shows `[YASLDR] execute finished with warnings`, it indicates that error data was encountered during the import process. Please check the log file for confirmation.

#### Why does it report the error `no free cursors in cursor pool` during the import?

This issue is caused by too high a current concurrency leading to insufficient cursors. You can adjust the system parameter SHARE_POOL_SIZE to increase the available buffer pool size or try again later.

#### Why does it report the error `no free blocks in large pool` during the import?

This issue is caused by a small setting for the parameter LARGE_POOL_SIZE in the target database. It is recommended to adjust LARGE_POOL_SIZE.
    
If this issue persists even after adjusting this parameter to the maximum value, it is recommended to:
1. Adjust the import table to reduce the total number of heaps partition tables.
2. Lower the degree_of_parallelism parameter during import.
3. Re-import.
    
#### Why does it report the error `the table is corrupted` during the import?
    
When the nologging parameter is set to true, if any data violates the constraint conditions of the target table during the import process, the import tool cannot provide fault tolerance and will report the error 'the table is corrupted'.
    
Please set nologging to false before importing again, or ensure the data meets the constraint conditions of the target table before re-importing.

#### Why does it report the error `dead lock detected` during the import?

This issue arises when there are indexes or constraints on the table to be imported. *yasldr* performs parallel imports, and if there are indexes or constraints, it may trigger this issue when the parallel threads insert data.

Please set the senders parameter to 1, or remove the indexes or constraints before re-importing.

#### Why does it report the error `no line break found in csv block buffer` during the import?

*yasldr* needs to load data from the disk into the memory buffer before it can parse line by line. If the data read into the buffer does not include a complete line, this error will be reported.

Please adjust the values of the csv_chunk_size or csv_line_size parameters so that the memory buffer can contain complete lines before executing the import again.

#### Why did the import duration surge?

During the import process, the system tables and views need to be queried to obtain metadata for the tables, and then the data is parsed and converted before being sent to the server. Possible reasons for the surge in import duration include:

- Frequent changes to the metadata, for example, in data migration evaluation scenarios with frequent creation and deletion of tables, leading to inaccurate current statistics and an inability to calculate the optimal execution plan; slow queries of metadata slow down the import. In this case, you can manually execute statistics collection and then retry the import.

- Statistics collection is running during the import process, causing database processes to compete for resources, slowing down data processing, and consequently slowing down the import. In this case, you can temporarily disable statistics collection and re-enable it after the import is finished.

#### Why is the import of time-series data into partition tables particularly slow?

*yasldr* organizes and manages the parsed csv data by partition during the import of partitioned data, with each partition pre-allocating a certain number of slots to store data. During time-series data import, the data collected by reader threads is likely to belong to the same partition, causing intense competition for partition slots.

Once the slots are used up, the reader must wait for the sender threads to finish sending before they can be used again, leading to the reader entering a waiting state due to unavailability of slots, which slows down the import speed.

To mitigate performance degradation due to partition conflicts in this business model, you can set the command line parameters SLICE_POLICY=RANDOM SENDER_TIMEOUT=0 during the import.