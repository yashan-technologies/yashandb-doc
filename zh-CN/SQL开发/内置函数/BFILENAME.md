```ebnf+diagram
bfilename::= BFILENAME "(" dir_expr "," file_expr ")"
```

BFILENAME函数返回一个BFILE定位器，其标识对应着服务器文件系统中的物理二进制文件。

若函数返回NULL，客户端将调整其返回格式为`BFILENAME(NULL)`。

**dir_expr**

dir_expr为字符型数据，其规格参考[DIRECTORY](../../全部手册/开发手册/SQL参考手册/SQL语句（yashan模式）/CREATE DIRECTORY)，其取值最大长度为64字节。

dir_expr为NULL时，函数返回NULL。

**file_expr**

file_expr为字符型数据，其取值最大长度为255字节。

file_expr为NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT BFILENAME('MY_DIR','test.pdf') res FROM DUAL;
RES
--------------------------------
bfilename('MY_DIR','test.pdf')

SELECT BFILENAME(NULL, NULL) res FROM DUAL;
RES
--------------------------------
bfilename(NULL)
```
