```ebnf+diagram
SYS_CONNECT_BY_PATH::= SYS_CONNECT_BY_PATH "(" colName "," delimiter ")"
```

SYS_CONNECT_BY_PATH为层次查询场景使用的函数，本函数返回从根节点到当前节点路径上所有节点名为colName的列的值，之间用指定的字符delimiter分隔开。

函数的返回值类型为VARCHAR，返回值最大长度32768，超长就会报错。

本函数不支持向量化计算。

**colName**

该参数为语句中表的列名。

**delimiter**

字符或字符串分隔符。只能为常量或常量的字符串。

示例（HEAP表）

```sql
DROP TABLE IF EXISTS area_info;
CREATE TABLE area_info (id INT, area_name VARCHAR(10), father_id INT);
INSERT INTO area_info VALUES(2, 'Zhejiang', 0);
INSERT INTO area_info VALUES(571, 'Hangzhou', 2);
INSERT INTO area_info VALUES(1, 'Guangdong', 0);
INSERT INTO area_info VALUES(755, 'Shenzhen', 1);
INSERT INTO area_info VALUES(756, 'Longhua', 755);
INSERT INTO area_info VALUES(757, 'Futian', 755);

SELECT  id, father_id, LEVEL,
CONNECT_BY_ROOT area_name AS name, 
SYS_CONNECT_BY_PATH(area_name, '——') path    
FROM area_info  
CONNECT BY PRIOR id = father_id START WITH father_id = 0  
ORDER SIBLINGS BY id DESC;  
      ID    FATHER_ID         LEVEL NAME          PATH
-------- ------------ ------------- ------------- --------------------------------------
       2            0             1 Zhejiang      ——Zhejiang
     571            2             2 Zhejiang      ——Zhejiang——Hangzhou
       1            0             1 Guangdong     ——Guangdong
     755            1             2 Guangdong     ——Guangdong——Shenzhen
     757          755             3 Guangdong     ——Guangdong——Shenzhen——Futian
     756          755             3 Guangdong     ——Guangdong——Shenzhen——Longhua
```
