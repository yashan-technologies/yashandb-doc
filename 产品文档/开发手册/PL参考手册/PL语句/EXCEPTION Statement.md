EXCEPTION Statement为异常处理语句，即exception handler，其语法形式为：

*EXCEPTION*  

*WHEN exception_name1 THEN  
statements*

*WHEN exception_name2 THEN*
*statements*

*...*

*WHEN OTHERS THEN* 

*statements;*

其中，WHEN后的exception_name可以使用or来连接多个异常，但是OTHERS不可与其他exception_name使用or连接。

在一个程序块'BEGIN END;'中，本语句只能出现一次。可以通过程序块的嵌套，来实现多层EXCEPTION Statement，此时，外层可以捕获内层执行过程中抛出的异常，且对于内层未捕获到的异常，外层可以继续捕获，直到该异常被成功捕获到，或者达到最外层仍未捕获到时，报出错误。

同一层的exception_name对应的错误码不可重复，不同层的exception_name对应的错误码则可以相同。

exception_name
---------------

已被定义的异常名称，用于过程体里抛出异常和exception handler捕获异常时进行一致识别的标识。

异常名称可以为系统预定义的异常名称，或者用户自定义的异常名称，具体描述见[异常处理](../PL异常处理)文档。

exception handler
-----------------

YashanDB对过程体中的异常处理，规则如下：

*   在执行过程中出错，并且错误码跟EXCEPTION Statement中某个WHEN后面的exception_name匹配，则执行其对应THEN后面的statements。
*   在执行过程中出错，并且错误码无法匹配到EXCEPTION Statement中所有WHEN后面的exception_name，则将其匹配到OTHERS，执行对应THEN后面的statements。
*   在执行过程中出错，并且错误码无法匹配到当前和外层过程体的EXCEPTION Statement中所有WHEN后面的exception_name，则作为未处理异常抛出。
*   执行过程中，如果出错并且捕获了异常；那在异常处理中出现的新错误，会将原先捕获的错误覆盖。  

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno IN OUT VARCHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
BEGIN
DBMS_OUTPUT.PUT_LINE(vno);
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
IF vno='01' THEN
vno := TO_NUMBER(vno)/0;
END IF;
IF vno='02' THEN
UPDATE area SET area_no='00' WHERE area_no=vno;             --由于'02'在子表上存在外键约束值，此语句将导致外键约束错误
END IF;
EXCEPTION
WHEN NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE('no data found');
WHEN ZERO_DIVIDE THEN
BEGIN
DBMS_OUTPUT.PUT_LINE('first:0 divide');
vno := TO_NUMBER(vno)/0;
EXCEPTION
WHEN NO_DATA_FOUND OR ZERO_DIVIDE THEN
DBMS_OUTPUT.PUT_LINE('second:0 divide');
END;
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE('unknown error');
END;
/
   
DECLARE
 a VARCHAR(2);
BEGIN
DBMS_OUTPUT.PUT_LINE('--------------------------');
a := '10';
ya_proc(a);                        --NO_DATA_FOUND
DBMS_OUTPUT.PUT_LINE('--------------------------');
a := '01';
ya_proc(a);                        --ZERO_DIVIDE
DBMS_OUTPUT.PUT_LINE('--------------------------');
a := '02';
ya_proc(a);                        --OTHERS
END; 
/
--result
--------------------------
10
no data found
--------------------------
01
num:01name:华东
first:0 divide
second:0 divide
--------------------------
02
num:02name:westofchina
unknown error
```
