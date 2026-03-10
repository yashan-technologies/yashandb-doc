DCL Statement为静态SQL执行语句。

COMMIT|ROLLBACK
---------------

COMMIT/ROLLBACK语法可参考相应的SQL语句（[COMMIT](../../SQL参考手册/SQL语句（yashan模式）/COMMIT)和[ROLLBACK](../../SQL参考手册/SQL语句（yashan模式）/ROLLBACK)），但在PL语句中无法使用FORCE子句。

在PL循环控制语句中COMMIT等同于COMMIT WRITE BATCH NOWAIT。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc IS
no VARCHAR(10);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
BEGIN
  
  
EXECUTE IMMEDIATE str1 INTO no,name USING '03';       --动态SQL
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
  
UPDATE area SET area_no='00' WHERE area_no='03';    --静态SQL
COMMIT;        --静态SQL
  
EXECUTE IMMEDIATE str1 INTO no,name USING '03' ;       --动态SQL,由于执行UPDATE语句，此语句将无结果返回
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
END;
/
   
exec ya_proc;
   
--result
区域号码:03区域名称:SouthChina
[9:1]YAS-05206 no data found
```

SAVEPOINT
---------

SAVEPOINT的语法形式为：

_BEGIN_  
_SAVEPOINT _savepoint_name_;_  
_..._  
_ROLLBACK TO _savepoint_name_;_  
_..._  
_END;_

示例

```plsql
--程序捕获到ZERO_DIVIDE异常，执行rollback，之前插入到area表的数据被回滚，所以第二次执行str1语句时抛出未找到数据的异常
DECLARE
a INT := 0;
b INT := 1;
c INT;
no VARCHAR(10);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=''99''';
BEGIN
SAVEPOINT start_trans;
c := 2;
INSERT INTO area VALUES('99','other',DEFAULT);
EXECUTE IMMEDIATE str1 INTO no,name ;
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
c := b/a;
EXCEPTION
WHEN ZERO_DIVIDE THEN
ROLLBACK TO start_trans;
EXECUTE IMMEDIATE str1 INTO no,name ;
DBMS_OUTPUT.PUT_LINE('区域号码:'||no||'区域名称:'||name);
END;
/
 
--result
区域号码:99区域名称:other
[18:1]YAS-05206 no data found
```
