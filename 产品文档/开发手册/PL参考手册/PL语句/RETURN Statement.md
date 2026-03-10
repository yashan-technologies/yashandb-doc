RETURN Statement为过程体结束控制语句，执行此语句时将会立即结束当前的过程体运行，并返回Statement内容到调用器。

RETURN Statement in function
----------------------------

在自定义函数中使用RETURN Statement的语法形式为：

_RETURN v_result;_

其中，v_result可以为变量、常量或表达式。

此语句不可以省略，即过程体中的每个执行路径（流程控制语句的分支，异常处理单元的分支）里都必须包含至少一个RETURN Statement，且v_result不可以省略，但v_result的值可以为NULL。

示例

```plsql
CREATE OR REPLACE FUNCTION ya_func RETURN INT IS
a VARCHAR(10) := '100';
BEGIN
    DBMS_OUTPUT.PUT_LINE('ya_func');
    RETURN TO_NUMBER(a)+10;
END;
/
 
SELECT TO_CHAR(ya_func) FROM dual;
 
--result
 
ya_func
TO_CHAR(YA_FUNC)
-------------------
110


-- EXCEPTION 异常处理
CREATE OR REPLACE FUNCTION ya_func (name_in IN VARCHAR) RETURN DATE IS
entry_out DATE;
BEGIN
    SELECT entry_date INTO entry_out FROM employees WHERE department NOT IN (SELECT deparment_no FROM department);
    RETURN(entry_out);
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE ('NO_DATA_FOUND');
        WHEN OTHERS THEN
            DBMS_OUTPUT.PUT_LINE ('Unexpected error');
END;
/


--result
SELECT ya_func('jack') FROM dual;
NO_DATA_FOUND

[11:1]YAS-05228 function must has return sql
```

RETURN Statement in others
----------------------------

在存储过程、匿名块、自定义高级包(body)、触发器中使用RETURN Statement的语法形式为：

_RETURN ;_

其中，return后不可跟变量、常量或表达式。

此语句可以省略，出现时用于表示后面的语句不再执行，结束程序。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
A VARCHAR(10) := '100';
BEGIN
    DBMS_OUTPUT.PUT_LINE('ya_proc 1');
    RETURN;
    DBMS_OUTPUT.PUT_LINE('ya_proc 2');
END;
/

--result
CALL ya_proc();
ya_proc 1
```
