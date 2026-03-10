The *yasboot* command can be used to collect logs from all nodes with one click. By default, it packages the logs from the nodes on the remote server and sends them to the current directory of the local server. You can customize the log storage directory using the -o parameter. For specific parameters and their meanings, please refer to [yasboot](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot cluster).

```shell
yasboot cluster log -c yashandb
start:  2023-07-28 00:00:00
end  :  2023-07-28 20:11:19
+-------+------------------+-------------+----------+---------------------------+---------+-------------+----------+------+
| type  | uuid             | name        | hostid   | index                     | status  | return_code | progress | cost |
+-------------------------------------------------------------------------------------------------------------------------+
| task  | 05fc032f6cc54f69 | LogCollect  | -        | yashandb                  | SUCCESS | 0           | 100      | 4    |
+-------+------------------+-------------+----------+---------------------------+---------+-------------+----------+------+
| child | 74413dd89a9de397 | LogCompress | host0001 | host0001                  | SUCCESS | 0           | 100      | 1    |
|       +------------------+-------------+----------+---------------------------+---------+-------------+----------+------+
|       | bd728249e323192e | LogDownload | host0001 | host0001                  | SUCCESS | 0           | 100      | 1    |
|       +------------------+-------------+----------+---------------------------+---------+-------------+----------+------+
|       | d08b47e74604478a | LogCombine  | -        | yashandb-05fc032f6cc54f69 | SUCCESS | 0           | 100      | 1    |
+-------+------------------+-------------+----------+---------------------------+---------+-------------+----------+------+
task completed, status: SUCCESS
download log to /var/lib/jenkins/anchorbase/work/bin
downloading...
download file block size: 2097152
```
