通用描述
----

ALTER SEQUENCE用于修改序列号生成器的各项参数。

存算一体分布式集群部署中用户无法执行本语句。

语句定义
----

**alter sequence::=**

```ebnf
= ALTER SEQUENCE [schema "."] sequence 
((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE) | RESTART [START WITH integer])
{" " ((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE) | RESTART [START WITH integer])}.
```

### INCREMENT BY

该语句用于修改序列号生成器的增量值，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)，修改后该序列器的开始值将不可回溯。

### MAXVALUE|NOMAXVALUE

该语句用于修改序列号生成器的最大值，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### MINVALUE|NOMINVALUE

该语句用于修改序列号生成器的最小值，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### CYCLE|NOCYCLE

该语句用于修改序列号生成器的循环开关设置，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### ORDER|NOORDER

该语句用于修改序列号生成器的ORDER属性，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### CACHE|NOCACHE

该语句用于修改序列号预分配个数，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### RESTART

该语句用于重置序列号生成器的序列号。默认情况下，升序序列号生成器将被重置到最小值，降序序列号生成器将被重置到最大值。

#### START WITH

该语句用于指定重置序列的初始值。指定该语句时，会将序列的序列号重置为给定值。

RESTART关键字与START WITH integer无强制先后顺序要求。

示例（单机/共享集群/分布式集群部署）

```sql
--创建一个最小值为10的升序序列号生成器
CREATE SEQUENCE seq_yashan MINVALUE 10;

SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   10
SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   11

--重置为最小值
ALTER SEQUENCE seq_yashan RESTART;

SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   10

--重置为指定值
ALTER SEQUENCE seq_yashan RESTART START WITH 11;

SELECT seq_yashan.NEXTVAL FROM DUAL;
   SEQ_YASHAN.NEXTVAL
---------------------
                   11
```
