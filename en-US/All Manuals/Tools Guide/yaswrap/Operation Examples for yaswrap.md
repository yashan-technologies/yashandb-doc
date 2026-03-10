1. Create a file containing PL source code and save it as plsql.sql. The file content is as follows:

    ```plsql
    $ vi plsql.sql

    CREATE OR REPLACE PROCEDURE ya_proc IS
    no VARCHAR(10);
    name VARCHAR(20);
    str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
    BEGIN
    SELECT area_no,area_name INTO no,name FROM area WHERE area_no='03';
    DBMS_OUTPUT.PUT_LINE('Region number:'||no||'Region name:'||name);

    UPDATE area SET area_no='00' WHERE area_no='03';    -- Static SQL
    commit;        -- Static SQL

    -- After the data is modified, the following statement will throw NO_DATA_FOUND exception, which will be captured by the EXCEPTION Statement
    SELECT area_no,area_name INTO no,name FROM area WHERE area_no='03';
    DBMS_OUTPUT.PUT_LINE('Region number:'||no||'Region name:'||name);
    EXCEPTION
    WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('warning:no data found!');
    END;
    /
    ```

2. Execute encryption:

    ```shell
    $ yaswrap iname=plsql oname=1
    YashanDB PL/SQL Wrapper: Release 23.2.1.100 x86_64

    Processing plsql.sql to 1.plb
    ```

3. Log in as the sales user to the *yasql* client and execute the encrypted file:

    ```sql
    @1.plb

    ```

4. Execute the stored procedure:

    ```sql
    SET serveroutput ON
    EXEC ya_proc;
    warning:no data found!

    ```
