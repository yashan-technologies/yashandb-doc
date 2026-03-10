DBMS_OUTPUT包提供了一组内置的存储过程，主要用于调试PL时输出变量、表达式的值等。

> **Note**:
>
> - 执行set serveroutput on命令会调用DBMS_OUTPUT.ENABLE(NULL)，并打开打印开关。在开关处于打开状态时，执行一条SQL语句或调用匿名PL后会调用DBMS_OUTPUT.GET_LINES()获取缓冲区内剩余的信息并打印。
> - 执行set serveroutput off命令会调用DBMS_OUTPUT.DISABLE()，并关闭打印开关。默认场景下缓冲区和打印开关都是关闭状态。
> - 客户端只有yasql支持set serveroutput选项。
> - 缓冲区每行大小不能超过65534bytes。
> - 存算一体分布式集群部署中，在CN节点上所有用户均可调用该高级包，但在DN/MN节点上必须使用sys用户方可调用该高级包。
> - HA环境中，只能在主库/主节点上调用该高级包。

## CHARARR

```plsql
TYPE CHARARR IS TABLE OF VARCHAR2(65534) INDEX BY BINARY_INTEGER;
```

CHARARR是DBMS_OUTPUT高级包内定义的数据类型，结合GET_LINES用于接收PUT、PUT_LINE输入缓冲区的信息。具体使用见GET_LINES示例。

## ENABLE

```plsql
DBMS_OUTPUT.ENABLE(
    buffer_size IN INTEGER DEFAULT 20000);
```

ENABLE存储过程启用缓冲区，使其能够被PUT、PUT_LINE、NEW_LINE、GET_LINE、GET_LINES存储过程操作。

|  参数| 描述|
| :---------- | :----------------------------------------------------------- |
| buffer_size | 启用的缓冲区大小（单位：字节），取值范围为[2000,1000000]，超出范围的数值按照较近的边界值处理，默认值为20000。若设置为NULL则等同于设置为4000000。 |

## DISABLE

```plsql
DBMS_OUTPUT.DISABLE();
```

DISABLE存储过程禁用缓冲区，禁止PUT、PUT_LINE、NEW_LINE、GET_LINE、GET_LINES存储过程操作缓冲区，并清除缓冲区中所有剩余信息。该存储过程无参数。

示例（单机、共享集群部署）

```plsql
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT('yashanDB');   
    DBMS_OUTPUT.PUT_LINE('hello world!');
    DBMS_OUTPUT.DISABLE();
END;        
/

--result
```

## PUT

```plsql
DBMS_OUTPUT.PUT(
    string IN VARCHAR2);
```

PUT存储过程向缓冲区输入一段无换行的文本。可以多次调用PUT，将多段信息拼接成一行。当使用PUT输入完一行后，需要调用NEW_LINE进行换行。GET_LINE、GET_LINES不会返回没有换行符的行。

## NEW\_LINE

```plsql
DBMS_OUTPUT.NEW_LINE();
```

NEW_LINE存储过程向缓冲区输入一个换行符。GET_LINE、GET_LINES返回以换行符分隔的行。每次调用PUT_LINE或NEW_LINE会生成可被GET_LINE或GET_LINES返回的一行。

## PUT\_LINE

```plsql
DBMS_OUTPUT.PUT_LINE(
    line IN VARCHAR2);
```

PUT_LINE存储过程向缓冲区输入一行。PUT_LINE输入的内容以换行符结尾，无需再调用NEW_LINE进行换行。

示例（单机、共享集群部署）

```plsql
SET serveroutput ON;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT('yashanDB');
    DBMS_OUTPUT.PUT_LINE('hello world!');
    DBMS_OUTPUT.PUT('coming'); 
END;
/

--result
yashanDBhello world!
```

## GET\_LINE

```plsql
DBMS_OUTPUT.GET_LINE(
    line OUT VARCHAR2,
    status OUT INTEGER);
```

GET_LINE存储过程从缓冲区获取一行以换行符结尾的信息，并丢弃换行符。当触发GET_LINE后，如果首次调用PUT、PUT_LINE或NEW_LINE，会先清空缓冲区再执行写入。

|  参数| 描述|
| :----- | :----------------------------------------------------------- |
| line   | 返回的缓冲区中的一行信息，不包括最后的换行符。               |
| status | 该次调用的状态。若调用执行成功，状态值置为0。若缓冲区已没有行，状态值置为1。 |

示例（单机、共享集群部署）

```plsql
SET serveroutput ON;

DECLARE
    line VARCHAR2(100);
    status INT;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT_LINE('Workshop');
    DBMS_OUTPUT.PUT_LINE('Workshop Man');
    DBMS_OUTPUT.GET_LINE(line,status);
    INSERT INTO department VALUES(status,line);
END;
/

SELECT deparment_no,department_name FROM department ORDER BY deparment_no;
DELETE FROM department WHERE deparment_no='0';

--result
Workshop Man

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
0            Workshop
000          Public Dep
002          Finance Dep
008          Purchasing Dep
010          Sales Dep

```

## GET\_LINES

```plsql
DBMS_OUTPUT.GET_LINES(
    lines OUT CHARARR,
    numlines IN OUT INTEGER);
```

GET_LINES存储过程从缓冲区获取行组成的数组。若缓冲区内剩余的行数不小于请求的行数，返回的行数即为请求的行数。若剩余的行数小于请求的行数，返回的行数为实际剩余的行数。当触发GET_LINES后，如果首次调用PUT、PUT_LINE或NEW_LINE，会先清空缓冲区再执行写入。

|  参数| 描述|
| :------- | :------------------------------------------- |
| lines    | 返回的缓冲区中的多行组成的数组。             |
| numlines | 想要从缓冲区获取的行数。返回实际获取的行数。 |

示例（单机、共享集群部署）

```plsql
DECLARE
    lines DBMS_OUTPUT.CHARARR;
    num INT;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT_LINE('Workshop');
    DBMS_OUTPUT.PUT_LINE('Workshop Man');
    num:=10;
    DBMS_OUTPUT.GET_LINES(lines,num);
    INSERT INTO department VALUES('050',lines(1));
    INSERT INTO department VALUES(num,lines(2));
END;
/
SELECT deparment_no,department_name FROM department ORDER BY deparment_no;
DELETE FROM department WHERE deparment_no IN ('050','2');

--result
DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
002          Finance Dep
008          Purchasing Dep
010          Sales Dep
050          Workshop
2            Workshop Man

```

## 异常说明

调用DBMS_OUTPUT高级包进行缓冲区写入时，若写入字符串长度超过上限会抛出如下异常：

|  错误码| 错误内容| 解释|
| :-------- | :-------------------------------------------------- | :--------------------------------------------- |
| YAS-30005 | line length overflow, limit of 65534 bytes per line | 缓冲区写入当前行导致溢出，每行上限为65534bytes |
| YAS-30006 | buffer overflow, limit of %d bytes                  | 缓冲区写入导致溢出，缓冲区总长度上限为%d bytes |
