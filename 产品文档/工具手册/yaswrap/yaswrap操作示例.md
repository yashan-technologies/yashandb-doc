1. 创建一个包含PL源代码的文件，保存为plsql.sql，文件内容如下：

    ```plsql
    $ vi plsql.sql

    CREATE OR REPLACE PROCEDURE ya_proc IS
    no VARCHAR(10);
    name VARCHAR(20);
    str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
    BEGIN
    SELECT area_no,area_name INTO no,name FROM area WHERE area_no='03';
    DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);

    UPDATE area SET area_no='00' WHERE area_no='03';    --静态SQL
    commit;        --静态SQL

    -- 数据被修改后，下面语句将抛出NO_DATA_FOUND异常，通过EXCEPTION Statement将其捕获
    SELECT area_no,area_name INTO no,name FROM area WHERE area_no='03';
    DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
    EXCEPTION
    WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('warning:no data found!');
    END;
    /
    ```

2. 执行加密：

    ```shell
    $ yaswrap iname=plsql oname=1
    YashanDB PL/SQL Wrapper: Release 23.2.1.100 x86_64

    Processing plsql.sql to 1.plb
    ```

3. 以sales用户登录yasql客户端，并执行加密文件：

    ```sql
    @1.plb

    ```

4. 执行存储过程：

    ```sql
    SET serveroutput ON
    EXEC ya_proc;
    warning:no data found!

    ```