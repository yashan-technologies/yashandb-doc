GOTO Statement为顺序控制语句，其语法形式为：

_<\<label_name>> statement_

_..._

_GOTO label_name；_

其中，<<>>用于为其后面的statement定义一个标签（Label），label_name为定义的标签的名字。

<\<label_name>>
---------------

用于为其后面的statement定义一个标签（Label），label_name为定义的标签的名称。

在同一个PL过程体里，标签的名称不能重复，否则报错。

  

标签可以位于过程体的任何位置，但如下位置除外：

*   END IF前面
*   END LOOP前面
*   END CASE前面
*   END前面
*   CASE Statement中的WHEN前面
*   EXCEPTION前面
*   过程体结束符'/'前面
*   匿名块的END后面

当为如下statement定义标签时：

*   以DECLARE开头的程序块，此时除了用于表示跳转位置外，与其连续使用的label_name还可作为这个程序块的名称来使用，例如通过label_name.variable_name，可以访问在这个程序块里声明的变量。
*   以FOR开头的循环语句，此时除了用于表示跳转位置外，label_name还可作为这个FOR语句的名称来使用，例如通过label_name.variable_name，可以访问在这个FOR语句里声明的变量。

GOTO label_name
----------------

GOTO label_name表示跳转到label_name标签所指向的statement，并从此statement开始往下执行。

当GOTO label_name位于某个PL语句的子句里面时，只能向外层跳转，而不能向同一层或更里层跳转。

示例

```plsql
--label\_name未与DECLARE开头的程序块连续使用
CREATE OR REPLACE PROCEDURE ya_proc(a VARCHAR, b VARCHAR) AS
BEGIN
    <<label1>>
    DBMS_OUTPUT.PUT_LINE(a);
    <<label2>>
        DECLARE
        a INT:=3;
        b INT:=3;
        BEGIN
            SELECT * INTO label1.a FROM dual;
            SELECT * INTO label2.b FROM dual;
        DBMS_OUTPUT.PUT_LINE('a+1 = '|| a);
        DBMS_OUTPUT.PUT_LINE('b+1 = '|| b);
        END;
    END;
/
YAS-04253 PL/SQL compiling errors:
[10:27] YAS-04243 invalid identifier "LABEL1"."A"

--label\_name与DECLARE开头的程序块连续使用
CREATE OR REPLACE PROCEDURE ya_proc(a VARCHAR, b VARCHAR) AS
BEGIN
    <<label1>>
    <<label2>>
        DECLARE
        a INT:=3;
        b INT:=3;
        BEGIN
            SELECT * INTO label1.a FROM dual;
            SELECT * INTO label2.b FROM dual;
        DBMS_OUTPUT.PUT_LINE('a+1 = '|| a);
        DBMS_OUTPUT.PUT_LINE('b+1 = '|| b);
        END;
    END;
/

CREATE OR REPLACE PROCEDURE ya_proc() IS
BEGIN
    CASE WHEN true THEN
    <<a>>
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN false THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
    GOTO a;
END;
/
YAS-04253 PL/SQL compiling errors:
[9:1] YAS-05223 invalid goto statement cannot jump to to label A
 

CREATE OR REPLACE PROCEDURE ya_proc() IS
BEGIN
    FOR i IN 1..3 LOOP
        DBMS_OUTPUT.PUT_LINE(i);
        GOTO a;
    END LOOP;
    <<a>>
    CASE WHEN true THEN
        DBMS_OUTPUT.PUT_LINE('hello');
    WHEN false THEN
        DBMS_OUTPUT.PUT_LINE('world');
    END CASE;
END;
/
  
exec ya_proc; 
  
--result
1
hello
```
