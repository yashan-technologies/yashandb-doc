YashanDB中的BFILE类型是一种可变长度数据类型，存储二进制大对象的特殊类型数据，主要用于处理存储在数据库外部的二进制文件。

## 存储属性

|  **类型**| 字节长度|
|--------|------------|
| BFILE  | 行存：最大328字节 |

YashanDB的BFILE类型通过行内存储操作系统中物理文件的引用，而非内容文件本身。文件实际存储在数据库之外的文件系统中，数据库仅记录文件路径和名称。

## 定义格式

|  **类型**| 格式| 规则|
|--------|-------|-----------------|
| BFILE  | BFILE | 变长二进制串，无需指定size |

## 使用规则

### 使用限制

- 仅适用于HEAP表。

- BFILE类型使用时需要创建文件路径别名，规格可参考[创建目录](../SQL语句（yashan模式）/CREATE DIRECTORY)。

- BFILE类型使用限制可参考[LOB使用限制](./大对象型.html#lob_use_restrictions)。

- BFILE字段仅支持读取操作系统文件，无法通过数据库修改其内容。文件的维护必须在数据库外部完成。

- BFILE类型不参与数据库事务，文件修改不会回滚，需手动保证数据一致性。

### 参数规格

BFILE打开的文件句柄数受会话级别参数SESSION_MAX_OPEN_FILES影响，默认值为50。

### 使用方法

BFILE类型通过内置函数[BFILENAME](../内置函数（yashan模式）/BFILENAME)定义BFILE定位器，进行插入操作，并通过[DBMS_LOB](../../PL参考手册/内置高级包/DBMS_LOB.html#dbms_lob_bfile)相关高级包功能进行文件数据的操作。

示例（HEAP表）

```sql
-- 1. 在sales用户下创建含有BFILE字段的tbl_bfile表
CREATE TABLE tbl_bfile (col1 bfile); 

-- 2. 创建对应的directory，并准备test.txt数据
CREATE OR REPLACE DIRECTORY my_dir AS '/data/yashan';
!echo "this is test bfile file here" >> /data/yashan/test.txt       

-- 2. 给tbl_bfile表增添数据
INSERT INTO tbl_bfile VALUES(BFILENAME('MY_DIR', 'test.txt'));

-- 3. 查看tbl_bfile表数据
SELECT * FROM tbl_bfile;

-- 4. 查看tbl_bfile表对应的文件数据
SELECT CAST(col1 AS VARCHAR(1000)) FROM tbl_bfile;
```
