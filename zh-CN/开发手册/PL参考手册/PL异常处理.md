在PL程序开发中，需要使用异常处理机制，对各类异常状况进行终止程序或错误规避的逻辑处理，而不是直接抛出错误信息和中止程序。

YashanDB提供如下三类异常：

- 系统预定义的异常，这类异常通常是一些触发了程序内部逻辑的错误，由系统抛出，过程体中可对其进行捕获，例如NO_DATA_FOUND未获得查询结果，LOGIN_DENIED鉴权不通过等。
- 用户自定义的异常，这类异常通常是开发人员依据业务逻辑规则所制定的错误，且须在过程体中自行抛出和捕获，例如身份证号码的合法性判断等。
- 不可预知的其他异常，开发人员无法预知到程序运行时可能会产生的错误，但可以通过OTHERS将其捕获，并进行友好型提示等逻辑处理。

无论上述何种异常，必须在过程体中进行主动捕获，才能接着对其进行逻辑处理，否则，系统将依据错误机制进行相应处理并抛出错误码和错误消息。

## 系统预定义的异常

YashanDB预定义了如下可以在过程体中被识别的异常名称：

|  内置异常名称| 说明|
|-----------------------| ----------------- |
| COLLECTION_IS_NULL  | 访问的数据集未初始化。 |
| CURSOR_ALREADY_OPEN | 重复打开游标。|
| DUP_VAL_ON_INDEX   | 发生索引冲突。|
| INVALID_CURSOR       | 使用无效的游标。|
| INVALID_NUMBER       | 使用非法数字。|
| LOGIN_DENIED         | 被拒绝登录。|
| NO_DATA_FOUND       | 在存储过程中查询数据为空。|
| NO_DATA_NEEDED      | 查询线程已不需要数据，管道函数停止写入。|
| ROWTYPE_MISMATCH     | 数据列类型不匹配。|
| STORAGE_ERROR        | 存储错误。 |
| SYS_INVALID_ROWID   | 使用非法ROWID。|
| TIMEOUT_ON_RESOURCE | 访问资源超时。|
| TOO_MANY_ROWS       | 在PL中指定select查询的数据返回了多行。|
| VALUE_ERROR          | 发生数值异常。|
| ZERO_DIVIDE          | 发生除零异常。|

系统预定义的异常由系统抛出，过程体中只需要进行捕获处理，具体操作可参考[EXCEPTION Statement](PL语句/EXCEPTION Statement)。

## 用户自定义的异常

用户自定义的异常需由用户在过程体中自行生成异常和抛出异常。分为如下两种方式：

**1.直接生成异常**

此方式通过[RAISE_APPLICATION_ERROR](#RAISEAPPLICATIONERROR)在需要的地方直接指定错误码和错误消息生成异常，并抛出该错误码和错误消息，无需指定异常名称，并且不对异常进行捕获。

**2.异常定义->异常初始化->异常抛出->异常接收**

此方式需首先进行异常的变量声明（异常定义），之后可对该异常进行错误码绑定（异常初始化），异常抛出和异常捕获（异常接收）等操作。

- 异常定义：声明自定义异常变量，具体操作可参考[自定义异常](PL语言基础/变量/自定义异常)文档说明。
- [异常初始化](#EXCEPTIONINIT)：对异常绑定错误码，不执行此操作则由系统自动生成错误码。
- [异常抛出](#RAISE)：可执行RAISE（捕获后向外层抛出），RAISE exception_name（抛出供本层捕获）两种不同的抛出。
- 异常接收：在异常句柄中捕获该异常并执行逻辑处理，具体操作可参考[EXCEPTION Statement](PL语句/EXCEPTION Statement)。

## OTHERS异常

对于开发人员无法预知的异常，或者过程体中未进行主动捕获的异常，都可以全部归集到OTHERS里进行处理，具体操作可参考[EXCEPTION Statement](PL语句/EXCEPTION Statement)。

## 错误码

YashanDB制定了一套错误码，用于对用户需要感知的错误进行抛出，此时用户只能接收到错误信息且程序被中止。异常处理机制则对指定的错误给予用户对其进行捕获处理的机会，每个异常需要对应相应的错误码。如下为YashanDB的错误码框架：

|  类别| 错误号区间范围| 对应方式|
| ---------------- | --------------- | ----------------------- |
| 系统预定义的错误 | (0,20000]     | --                      |
| 系统预定义的异常 | [20000,21000) | 系统生成                |
| 用户自定义的异常 | [21000,30000) | EXCEPTION变量声明       |
| 用户自定义的异常 | [30000,100000) | RAISE_APPLICATION_ERROR |
| 系统包预定义的错误 | [100000,109999] | 系统生成 |
| 用户自定义的异常 | (0,109999]     | EXCEPTION_INIT          |

预定义的错误请查阅[错误码](../../参考手册/错误码)。

<span id="RAISEAPPLICATIONERROR" name="RAISEAPPLICATIONERROR" class="yaslink"></span>

### RAISE\_APPLICATION\_ERROR

RAISE_APPLICATION_ERROR为系统提供的异常处理函数，其语法格式为：

*RAISE_APPLICATION_ERROR(errCode, errMsg);*

**errCode**

错误号，需指定为上表范围内的值，否则在执行程序时系统将抛出YAS-04426错误。

**errMsg**

错误消息。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
BEGIN
IF vno>'10' THEN
RAISE_APPLICATION_ERROR(40000,'no data found for '||vno);
END IF;
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
END;
/

exec ya_proc('99');
--result
YAS-40000 no data found for 99
```

<span id="EXCEPTIONINIT" name="EXCEPTIONINIT" class="yaslink"></span>

### EXCEPTION\_INIT

EXCEPTION_INIT为系统内置程序块，使用该程序块的语法格式为：

*PRAGMA EXCEPTION_INIT(exception_name,errCode)*;

对用户声明的EXCEPTION变量，使用EXCEPTION_INIT可以对其绑定一个错误码，否则系统将按上表范围自动生成一个错误码。

**exception_name**

已被声明的变量名称。

**errCode**

绑定的错误码，需指定为上表范围内的值，否则编译报错。

> **Caution**: 
> 
> 禁止定义2017错误码：
>
>2017错误码表示DC失效，依据YashanDB内部处理逻辑，在DC失效时会进行reparse后再执行。如手动raise这个错误码且无异常句柄接收，将导致系统陷入不断reparse和执行的死循环。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
err EXCEPTION;
PRAGMA EXCEPTION_INIT(err, 45000);
BEGIN
IF vno>'10' THEN
RAISE err;
END IF;
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
EXCEPTION
WHEN err THEN
DBMS_OUTPUT.PUT_LINE('no data found for '||vno);
END;
/

exec ya_proc('99');
--result
no data found for 99
```

### SQLCODE&SQLERRM

SQLCODE和SQLERRM为系统提供的异常处理函数，用于获取异常对应的错误号和错误消息，其语法格式为：

*SQLCODE[()]*

*SQLERRM[(errCode)]*

SQLCODE和SQLERRM只能用于过程性语句中，不能用于SQL语句中。当被用于非异常处理的语句中时，SQLCODE函数将返回0值，SQLERRM函数将返回"YAS-00000 normal, successful completion"。

当被用于异常处理语句中时，SQLCODE函数将返回当前捕获到的异常错误号，SQLERRM函数返回规则如下：

- 不指定errCode参数时，返回当前错误对应的错误消息。
- 指定errCode参数时，如errCode超出上表指定范围，返回"%d non-yas exception"，否则查找errCode对应的错误消息并返回。
- 当前错误号或errCode未定义错误消息时，返回”YAS-%05d message %05d not found“。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
err EXCEPTION;
PRAGMA EXCEPTION_INIT(err, 45000);
BEGIN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
IF vno>'10' THEN
RAISE err;
END IF;
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
EXCEPTION
WHEN err THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
END;
/

exec ya_proc('99');
--result
0:YAS-00000 normal, successful completion
45000:YAS-45000 message 45000 not found
```

### STANDARD

STANDARD用于表明其后面的exception_name为系统预定义的异常名称，当用户自定义的异常可能与系统预定义的异常重名时，使用STANDARD可以准确识别出系统预定义的异常。语法格式为：

*STANDARD.exception_name*

exception_name必须为系统预定义异常的名称，否则将出现编译错误。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
no_data_found EXCEPTION;
PRAGMA EXCEPTION_INIT(no_data_found, 45000);
BEGIN
IF vno>'10' THEN
RAISE no_data_found;
ELSE 
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
END IF;
EXCEPTION
WHEN STANDARD.NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE('Standard exception.');
WHEN NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
END;
/

exec ya_proc('99');
--result
45000:YAS-45000 message 45000 not found

exec ya_proc('09');
--result
Standard exception.
```

<span id="RAISE" name="RAISE" class="yaslink"></span>

### RAISE

RAISE语句用于显式地抛出异常，其语法格式为：

*RAISE [exception_name];*

当不指定exception_name时，该语句必须位于异常句柄（EXCEPTION Statment）中，表示将当前的异常继续向外层抛出。

exception_name可以为用户自定义的异常，也可以是系统预定义的异常。

当本层的异常句柄并没有捕获RAISE后的exception_name时，该异常将向外层抛出，对与用户自定义的异常同名的系统预定义的异常，则需要使用STANDARD捕获。

示例

```plsql
CREATE OR REPLACE PROCEDURE ya_proc(vno CHAR) IS
no VARCHAR(2);
name VARCHAR(20);
str1 VARCHAR(100) := 'select area_no,area_name from area where area_no=:a';
no_data_found EXCEPTION;
BEGIN
IF vno>'10' THEN
BEGIN
DBMS_OUTPUT.PUT_LINE('First:');
RAISE no_data_found;
EXCEPTION
WHEN NO_DATA_FOUND THEN
RAISE;
END;
ELSE
DBMS_OUTPUT.PUT_LINE('Second:');
EXECUTE IMMEDIATE str1 INTO no,name USING vno;      
DBMS_OUTPUT.PUT_LINE('num:'||no||'name:'||name);
END IF;
EXCEPTION
WHEN STANDARD.NO_DATA_FOUND THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
WHEN OTHERS THEN
DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
END;
/

exec ya_proc('99');
--result
First:
21000:YAS-21000 message 21000 not found

exec ya_proc('09');
--result
Second:
5206:YAS-05206 no data found
```
