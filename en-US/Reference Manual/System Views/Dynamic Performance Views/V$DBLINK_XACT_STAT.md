This view displays the relevant statistics of all database links in the connection group on the sandbox process yex_server and their corresponding links.

|Field |Type |Description |
|------------------------|---------|----------------------------------|
| EXT\_XACT\_ID           | INTEGER  | Connection group ID                        |
| EXT\_CONNECTION\_COUNT  | INTEGER  | The number of connections established with the remote database within the connection group |
| EXT\_XACT\_NEXT\_ID     | INTEGER  | The ID of the next connection group; if the value is -1, it indicates that the current connection group is in use; if the value is 1024, it indicates the last connection group in the idle connection groups |
| EXT\_XACT\_IS\_FREE     | BOOLEAN  | Indicates whether the connection group is idle; TRUE means idle, FALSE means in use |