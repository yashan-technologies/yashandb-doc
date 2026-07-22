```ebnf
vector_serialize = VECTOR_SERIALIZE "(" expr [ RETURNING ( CLOB | VARCHAR  "(" size [BYTE | CHAR] ")" ) ] ")".

```
VECTOR_SERIALIZE函数用于将向量数据或向量形式的字符串数据转换为VARCHAR或者CLOB类型数据。

VECTOR_SERIALIZE函数与[FROM_VECTOR](FROM_VECTOR.md)函数含义相同。
