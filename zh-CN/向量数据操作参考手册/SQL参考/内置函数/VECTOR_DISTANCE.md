```ebnf
vector_distance = VECTOR_DISTANCE "(" expr1 "," expr2 [ "," metric ] ")".

l2_distance = L2_DISTANCE "(" expr1 "," expr2 ")".

inner_product = INNER_PRODUCT "(" expr1 "," expr2 ")".

cosine_distance = COSINE_DISTANCE "(" expr1 "," expr2 ")".
```

VECTOR_DISTANCE函数用于两个向量之间的距离，函数返回值为DOUBLE类型。

使用VECTOR_DISTANCE函数查询时，如果对应列上创建了索引，且向量距离计算方法相同，才会通过索引加速查询。


**expr**

表示输入的向量类型数据或向量格式的CHAR/VARCHAR/CLOB类型数据。

- 输入CHAR/VARCHAR/CLOB类型的向量数据时，必须是一组包裹在`[]`内的非空数组，且数值不允许为nan和inf。
- 两个expr的维度数据类型和维度数必须一致。
- 当某个expr输入为NULL时，函数返回NULL。


**metric**

支持以下五种向量距离计算方法。

- EUCLIDEAN：表示计算两个向量的欧式距离。
- EUCLIDEAN_SQUARED：表示计算两个向量的不取平方根的欧式距离。
- L2_SQUARED：同EUCLIDEAN_SQUARED。
- COSINE：表示计算两个向量的余弦距离，与独立函数COSINE_DISTANCE为同义词。
- DOT：表示计算两个向量的负内积距离。

当不配置metric时，会根据入参的向量列上向量索引的情况判断启用那种距离计算方法。

- 两个expr不存在向量索引时，使用COSINE距离算法。
- 只有一个expr存在向量索引时，使用该向量索引的距离算法。
- 两个expr均存在向量索引且索引的距离计算方法一致时，使用该距离算法。
- 两个expr均存在向量索引且索引的距离计算方法不一致时，使用COSINE距离算法。
- 输入的expr为子查询时，判断该expr不存在索引，距离算法遵循上述判断规则。

示例（HEAP表）

```sql
-- expr: 为char类型

CREATE TABLE chtb(a char(10));
INSERT INTO chtb values('[1,1,1]');
SELECT VECTOR_DISTANCE(chtb.a, '[1,1,1]') FROM chtb;

VECTOR_DISTANCE(CHTB.A,'[1,1,1]')
---------------------------------
​                               0

-- expr: 为varchar类型

CREATE TABLE vctb(a varchar(10));
INSERT INTO vctb values('[1,1,1]');

SELECT VECTOR_DISTANCE(vctb.a, '[1,1,1]') FROM vctb;
VECTOR_DISTANCE(VCTB.A,'[1,1,1]')
---------------------------------
​                0

-- expr: 为clob类型

CREATE TABLE ctb(a clob);
INSERT INTO ctb values('[1,1,1]');
SELECT VECTOR_DISTANCE(ctb.a, '[1,1,1]') FROM ctb;
VECTOR_DISTANCE(CTB.A,'[1,1,1]')
--------------------------------
​                0

-- 输入参数向量值数据类型不一样，执行报错

CREATE TABLE vt(a vector(3, FLOAT32));
CREATE TABLE vt2(a vector(3, FLOAT64));
INSERT INTO vt values('[1,1,1]');
INSERT INTO vt2 values('[2,2,2]');

SELECT l2_distance(vt.a, vt2.a) FROM vt, vt2;

[1:8]YAS-00906 requires all input vectors to have the same dimension format : encountered dimension formats (FLOAT32, FLOAT64)

-- 输入参数向量维度不一样，执行报错

SELECT l2_distance('[1,1,1]', '[2,2]') FROM dual;

[1:8]YAS-00905 vector dimension count mismatch (3, 2)
```
