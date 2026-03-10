默认情况下，用户创建的各项PL对象源代码将作为文本被存储在数据库中。为避免其他人通过数据字典获取该类文本，可以对PL对象的源代码进行加密。

## 可加密对象

支持源码加密的PL对象包括：

- 自定义高级包（PACKAGE HEAD、PACKAGE BODY）

- 自定义函数

- 存储过程

对于其他对象，例如触发器，可以先将其封装进某个存储过程中，再通过对该存储过程的源码进行包装加密从而实现对触发器源码的加密。

## 加密工具yaswrap

[yaswrap](../../工具手册/yaswrap/00yaswrap)是YashanDB提供的专用于对PL源代码文本进行加密包装的工具。

- 用户可以使用该工具对指定代码文件进行加密并输出包装后的文件，避免他人通过数据库视图（例如*_SOURCE）查看到PL源码。

- 用户可以直接执行yaswrap加密后的密文创建PL对象（数据库表中存储的也是密文），该PL对象后续仍可正常执行，用户对系统的加/解密无感知。

用户如需重建一个加密包装后的PL对象，不允许直接在数据库中编辑（直接编辑会导致过程体无法正常执行），需先修改源文本文件再重新使用yaswrap进行加密包装。

>**Caution**:
>
> 不建议使用PL源码加密方式保护/隐藏密码或表名。

## 使用示例

如下为原始PL语句，保存在mywrap.sql文件中：

```plsql
CREATE OR replace FUNCTION myAdd(a INT, b INT) RETURN INT AS
BEGIN 
RETURN a + b;
END;
/
```

1. 使用yaswrap工具对mywrap.sql进行包装，生成包装后的mywrap.plb文件：

  ```shell
  $ yaswrap iname=mywrap.sql
  YashanDB PL Wrapper: Debug 22.1.0.15 x86_64

  Processing mywrap.sql to mywrap.plb
  ```

2. 生成的mywrap.plb内容：

  ```plsql
  CREATE OR replace FUNCTION myAdd WRAPPED
  a000000
  367
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  abcd
  8
  45 75
  a0Q4UMeqLlU7qUUUQUA1P6sY7bCHrTewXzfgH09/NYzaR8SUHYRFD0+wHRWEIBWpoBTqEhoU
  5wJrKDYDeTm6u3r5IHFFAxUUtRKcqbxd/FypEABpYBGc

  /
  ```

3. 通过yasql执行加密后的PL语句：

    ```plsql
    -- 执行包装后的PL语句
    CREATE OR replace FUNCTION myAdd WRAPPED
    a000000
    367
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    8
    45 75
    a0Q4UMeqLlU7qUUUQUA1P6sY7bCHrTewXzfgH09/NYzaR8SUHYRFD0+wHRWEIBWpoBTqEhoU
    5wJrKDYDeTm6u3r5IHFFAxUUtRKcqbxd/FypEABpYBGc

    /


    SELECT myAdd(1, 2) FROM dual;

        MYADD(1,2)
    ------------
                3


    -- 从USER_SOURCE中查看function的文本

    SELECT text FROM USER_SOURCE WHERE name = 'MYADD';

    TEXT
    ----------------------------------------------------------------
    CREATE OR replace FUNCTION myAdd WRAPPED
    a000000
    354
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    abcd
    8
    46 79
    VNCA8yPUw4/UNq3WZDDPDrXdbhkwgwzV1wzR+kdyXoU79Vmhs1rDhUfVs8xa4czsnukmbGPp
    Qsph5uB/6gknI8lB4R9JwyHMNbqo+uzVKpaV7Pumk7Jvwg==

    ```
