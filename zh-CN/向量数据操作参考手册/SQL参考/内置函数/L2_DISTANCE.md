```ebnf
l2_distance = L2_DISTANCE "(" expr1 "," expr2 ")".
```

L2_DISTANCE函数用于两个向量之间的欧式距离，函数返回值为DOUBLE类型。

L2_DISTANCE函数是VECTOR_DISTANCE函数**metric**配置为`EUCLIDEAN`时的简化用法。

使用L2_DISTANCE函数查询时，如果对应列上创建了基于欧式距离算法或欧氏距离平方算法的索引，都可以通过索引加速查询。


**expr**

表示输入的向量类型数据或向量格式的CHAR/VARCHAR/CLOB类型数据。

- 输入CHAR/VARCHAR/CLOB类型的向量数据时，必须是一组包裹在`[]`内的非空数组，且数值不允许为nan和inf。
- 两个expr的维度数据类型和维度数必须一致。
- 当某个expr输入为NULL时，函数返回NULL。

