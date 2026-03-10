通用描述
----

ALTER SEQUENCE用于修改序列号生成器的各项参数。

存算一体分布式集群部署中用户无法执行本语句。

语句定义
----

**alter sequence::=**

```ebnf+diagram
syntax::= ALTER SEQUENCE [schema "."] sequence 
((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE))
{" " ((INCREMENT BY integer) | (MAXVALUE integer|NOMAXVALUE) | (MINVALUE integer|NOMINVALUE) | (CYCLE|NOCYCLE) | (ORDER|NOORDER) | (CACHE integer|NOCACHE))}
```

### 1. INCREMENT BY

该语句用于修改序列号生成器的增量值，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)，修改后该序列器的开始值将不可回溯。

### 2. MAXVALUE|NOMAXVALUE

该语句用于修改序列号生成器的最大值，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### 3. MINVALUE|NOMINVALUE

该语句用于修改序列号生成器的最小值，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### 4. CYCLE|NOCYCLE

该语句用于修改序列号生成器的循环开关设置，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### 5. ORDER|NOORDER

该语句用于修改序列号生成器的ORDER属性，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。

### 6. CACHE|NOCACHE

该语句用于修改序列号预分配个数，规则同[CREATE SEQUENCE](./CREATE SEQUENCE)。
