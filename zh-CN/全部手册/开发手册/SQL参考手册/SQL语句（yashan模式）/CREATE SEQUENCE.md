通用描述
----

CREATE SEQUENCE用于创建一个序列对象，包括升序序列号生成器（生成的序列号为正数）和降序序列号生成器（生成的序列号为负数）。

序列号生成器的数据类型只能为整数。

对于升序序列号生成器，生成的序列号不能小于1；对于降序序列号生成器，生成的序列号不能大于-1。

存算一体分布式集群部署中用户无法执行本语句。

语句定义
----

**create sequence::=**

```ebnf+diagram
syntax::= CREATE SEQUENCE [schema "."] sequence 
[(((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE)) 
{" " (((INCREMENT BY|START WITH) integer)|(MAXVALUE integer|NOMAXVALUE)|(MINVALUE integer|NOMINVALUE)|(CYCLE|NOCYCLE)|(ORDER|NOORDER)|(CACHE integer|NOCACHE))}]
```

### 1. sequence

该语句用于指定要创建的序列号生成器的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

如该语句后面的语句全部省略，则会生成一个从1开始，每次增长1，无最大数限制（但满足操作系统最大整数限制）的升序序列号生成器。

序列号分为CURRVAL和NEXTVAL两种类型，具体描述见[伪列](../基本SQL元素/伪列)章节描述。

示例（单机、共享集群部署）

```sql
CREATE SEQUENCE seq_yashan1;
 
SELECT seq_yashan1.NEXTVAL FROM DUAL;
  SEQ_YASHAN1.NEXTVAL
---------------------
                    1
```

### 2. INCREMENT BY

该语句用于指定序列号生成器的增量（Interval），即下一序列号值=当前序列号值+增量。该值不能为0，省略则默认为1。

本值为正数即表示创建的是一个升序序列号生成器，为负数则表示创建的是一个降序序列号生成器。

### 3. START WITH

该语句用于指定序列号生成器的起始值，该值需介于MINVALUE和MAXVALUE之间。省略则默认为1（升序序列号生成器）或-1（降序序列号生成器）。

### 4. MAXVALUE|NOMAXVALUE

该语句用于指定序列号生成器的最大值。

对于升序序列号生成器，NOMAXVALUE表示为操作系统的最大正整数数值；对于降序序列号生成器，NOMAXVALUE表示为-1。

本语句省略时默认为NOMAXVALUE。

### 5. MINVALUE|NOMINVALUE

该语句用于指定序列号生成器的最小值。

对于升序序列号生成器，NOMINVALUE表示为1；对于降序序列号生成器，NOMAXVALUE表示为操作系统的最大负整数数值。

本语句省略时默认为NOMINVALUE。

### 6. CYCLE|NOCYCLE

该语句用于指定当生成的序列号到达MAXVALUE（升序序列号生成器）或MINVALUE（降序序列号生成器）后，是否开启序列号循环。

- CYCLE：对于升序序列号生成器，当要生成的序列号大于MAXVALUE时，将该序列号置为MINVALUE；对于降序序列号生成器，当要生成的序列号小于MINVALUE时，将该序列号置为MAXVALUE。

- NOCYCLE：当要生成的序列号大于MAXVALUE（升序序列号生成器）或小于MINVALUE（降序序列号生成器）时，不产生值并返回错误信息。

本语句省略时默认为NOCYCLE。

示例（单机、共享集群部署）

```sql
--创建一个最大值为20的升序序列号生成器
CREATE SEQUENCE seq_yashan3 INCREMENT BY 10 MAXVALUE 20 CYCLE NOCACHE;
 
--当要生成的序列号超过20时，重新从最小值1开始
SELECT seq_yashan3.NEXTVAL FROM DUAL;
  SEQ_YASHAN3.NEXTVAL
---------------------
                    1
SELECT seq_yashan3.NEXTVAL FROM DUAL;
  SEQ_YASHAN3.NEXTVAL
---------------------
                   11
SELECT seq_yashan3.NEXTVAL FROM DUAL;
  SEQ_YASHAN3.NEXTVAL
---------------------
                    1

--创建一个含自增列的表
CREATE TABLE area_seq(area_no INT DEFAULT seq_yashan3.NEXTVAL, area_name VARCHAR2(60));
```
<span id="sequence_order" name="sequence_order" class="yaslink"></span>

### 7. ORDER|NOORDER

ORDER/NOORDER用于指定是否保证序列号按请求的先后顺序生成。

- 在单机部署中，YashanDB已保证了序列号按照请求的先后顺序生成，ORDER/NOORDER对序列号的表现没有影响。

- 在共享集群部署中，如果指定了ORDER，序列号将在所有实例上保持有序；如果指定了NOORDER，且预分配序列号个数大于1，那么序列号将在每个实例内保持有序。

示例（共享集群部署）

在两实例共享集群部署中，实例1执行：

```sql
-- 创建一个预分配5个序列号的有序序列
CREATE SEQUENCE seq_yashan4_order CACHE 5 ORDER;

-- 创建一个预分配5个序列号的无序序列
CREATE SEQUENCE seq_yashan4_noorder CACHE 5 NOORDER;

-- 在实例1获取这两个序列的序列号
SELECT seq_yashan4_order.NEXTVAL FROM DUAL;
SEQ_YASHAN4_ORDER.NE
--------------------
                   1
SELECT seq_yashan4_noorder.NEXTVAL FROM DUAL;
SEQ_YASHAN4_NOORDER.
--------------------
                   1
```

实例2执行：

```sql
-- 在实例2获取这两个实例的序列号
SELECT seq_yashan4_order.NEXTVAL FROM DUAL;
SEQ_YASHAN4_ORDER.NE
--------------------
                   2
SELECT seq_yashan4_noorder.NEXTVAL FROM DUAL;
SEQ_YASHAN4_NOORDER.
--------------------
                   6
```

### 8. CACHE|NOCACHE

该语句用于指定是否在内存中对序列号进行预分配，省略则默认为在内存中预分配20个序列号。

- NOCACHE：不在内存中进行序列号预分配。

- CACHE：指定预分配序列号的个数，该值可为任意一个大于1的整数。当同时指定CYCLE时，该值还需小于序列号生成器一个循环内可产生的序列号个数，即该值需小于`(MAXVALUE-MINVALE)/ABS(INCREMENT BY)+1`。

示例（单机、共享集群部署）
```sql
 --由于((11-1)/10)+1=2,而cache值需小于此值，返回错误
CREATE SEQUENCE seq_yashan5 INCREMENT BY 10 MAXVALUE 11 CYCLE CACHE 2;
YAS-02096 sequence param CACHE error, number of CACHE must be less than one cycle
```

在共享集群部署中，预分配是实例级别的，各实例按先后顺序预分配一段序列号，序列号在实例内有序，在集群全局不保证有序。

如果指定为NOCACHE，则序列不进行预分配，序列号在集群全局上有序。

示例（共享集群部署）

**预分配序列号场景**

在两实例共享集群部署中，实例1执行：

```sql
CREATE SEQUENCE seq_yashan6 CACHE 5;
-- 实例1预分配了序列号1、2、3、4、5
SELECT seq_yashan6.NEXTVAL FROM DUAL;
SEQ_YASHAN6.NEXTVAL
-------------------
                  1
```

实例2执行：

```sql
-- 实例2在实例1预分配之后，预分配了序列号6、7、8、9、10
SELECT seq_yashan6.NEXTVAL FROM DUAL;
SEQ_YASHAN6.NEXTVAL
-------------------
                  6
```

**不进行预分配序列号场景**

在两实例共享集群部署中，实例1执行：

```sql
CREATE SEQUENCE seq_yashan7 NOCACHE;
-- 实例1获取序列号1
SELECT seq_yashan7.NEXTVAL FROM DUAL;
SEQ_YASHAN7.NEXTVAL
-------------------
                  1
```

实例2执行：

```sql
-- 实例2在实例1获取序列号之后，获取到2
SELECT seq_yashan7.NEXTVAL FROM DUAL;
SEQ_YASHAN7.NEXTVAL
-------------------
                  2
```
