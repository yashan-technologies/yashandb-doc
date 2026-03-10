EXECUTE Statement为动态SQL执行语句，其常规的语法形式为：

*EXECUTE IMMEDIATE v_sql;*

其中，v_sql为SQL语句形式的字符串变量或常量。在YashanDB里，v_sql除了可以为普通SQL语句外，还可以为"BEGIN..END;"形式的匿名块。

## 静态SQL与动态SQL

静态SQL：在过程体创建时就已经确定好的SQL语句，例如[DML Statement](./DML Statement)和[DCL Statement](./DCL Statement)。

动态SQL：在过程体运行时才能确定的SQL语句，例如查询的字段、条件等信息来自于用户的输入，或者只能根据程序逻辑来动态构造。

EXECUTE Statement里的v_sql为动态SQL。

## 常规用法

*EXECUTE IMMEDIATE v_sql;*

执行v_sql字符串里包含的SQL语句。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
BEGIN
EXECUTE IMMEDIATE 'alter table area drop DHQ';
END;
/
exec ya_proc;
```

## INTO用法

*EXECUTE IMMEDIATE v_sql INTO v1,v2,...;*

'v1,v2...'为已声明的变量。

执行v_sql字符串里包含的SQL语句（只能是SELECT语句），并将查询结果赋值给变量，'v1,v2...'与SELECT语句的查询项一一对应。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where rownum=1';
BEGIN
EXECUTE IMMEDIATE str1 INTO no,name ;
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
END;
/
exec ya_proc;
 
--result
区域号码:01区域名称:华东
```

## USING用法

*EXECUTE IMMEDIATE v_sql  USING \[IN|OUT|IN OUT\] a1,a2,...;*

a1,a2...为变量或常量形式的[绑定参数](../参数/绑定参数)。

> **Note**：
> v_sql是匿名块且使用`:name` 形式占位符，同名的占位符只需要绑定一次即可。

IN|OUT|IN OUT为绑定参数类型，默认为IN类型。规则如下：

*   IN参数可以为变量、变量表达式或常量；
*   OUT和IN OUT参数只能为已声明的变量；
*   当v_sql里包含了对另一个过程体的调用时，对绑定参数的类型使用与该过程体定义的参数类型一致。

当USING和INTO同时使用时，INTO语句应该位于USING语句前面。

示例

```plsql
--v_sql为DML语句
CREATE OR REPLACE PROCEDURE ya_proc() IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no in (:a,:b) and rownum=1';
BEGIN
EXECUTE IMMEDIATE str1 INTO no,name USING '01','02';
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
END;
/
exec ya_proc;
 
--result
区域号码:01区域名称:华东
```

示例（HEAP表）

```plsql
--insert into...return into...
CREATE OR REPLACE PROCEDURE ya_proc IS
no INT;
area_name VARCHAR(20);
str1 VARCHAR(200) := 'INSERT INTO area(area_no) VALUES(:a) RETURN TO_NUMBER(area_no), NVL(area_name, ''area''||area_no) INTO :b, :c';
BEGIN
EXECUTE IMMEDIATE str1 USING '09',out no,out area_name;
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||area_name);
END;
/
exec ya_proc;
 
--result
区域号码:9区域名称:area09
```

示例

```plsql
--v_sql为调用过程体语句
--定义一个存储过程，其中a2的参数在过程体被赋值输出
CREATE OR REPLACE PROCEDURE ya_block(a1 VARCHAR,a2 IN OUT VARCHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no in (:a,:b) and rownum=1';
BEGIN
EXECUTE IMMEDIATE str1 INTO no,name USING a1,a2;
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
a2 := '00';
END;
/
--通过匿名块中调用
DECLARE
a1 VARCHAR(2):='01';
a2 VARCHAR(2):='02';
BEGIN
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
ya_block(a1,a2);
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
END;
/
--result
a1:01,a2:02
区域号码:01区域名称:华东
a1:01,a2:00
 
--通过动态SQL调用
CREATE OR REPLACE PROCEDURE ya_proc IS
a1 VARCHAR(2):='01';
a2 VARCHAR(2):='02';
BEGIN
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
EXECUTE IMMEDIATE 'begin ya_block(:a,:b); end;' USING a1,IN OUT a2;
DBMS_OUTPUT.PUT_LINE('a1:'||a1||',a2:'||a2);
END;
/
exec ya_proc;
--result
a1:01,a2:02
区域号码:01区域名称:华东
a1:01,a2:00
```
