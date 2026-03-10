```ebnf+diagram
load_file::= LOAD_FILE "(" path_expr ")"

```
LOAD_FILE函数用于读取文件并以BLOB形式返回文件内容。

如需使用本函数，需满足如下前提条件：

- 目标文件必须位于数据库所在服务器上，且文件必须是所有人可读。

- 如果secure_file_priv系统变量被设置为非空目录名，则要求目标文件必须位于该目录中。

- 数据库用户必须具有FILE权限。

**path_expr**

path_expr必须指定为目标文件的完整路径。

如果目标文件不存在，函数将返回NULL。

示例（单机HEAP表）

```sql

ALTER SYSTEM SET secure_file_priv='/data/' scope=memory;


!rm -rf /data/testFile.txt
‍️!echo 'abcd💖😜😊🤷‍♀️✔（*＾-＾*）精神状态稳定' >> /data/testFile.txt
!echo '123' >> /data/testFile.txt
!echo 'abcd' >> /data/testFile.txt
SELECT LOAD_FILE('/data/testFile.txt') FROM dual;

load_file('/data/testFile.txt')                                  
---------------------------------------------------------------- 
61626364EDA0BDEDB296EDA0BDEDB89CEDA0BDEDB88AEDA0BEEDB4B7E2808DE29980EFB88FE29C94EFBC882AEFBCBE2DEFBCBE2AEFBC89E7B2BEE7A59EE78AB6E68081E7A8B3E5AE9A0A3132330A616263640A


```
