```ebnf
vector_serialize = VECTOR_SERIALIZE "(" expr [ RETURNING ( CLOB | VARCHAR  "(" size [BYTE | CHAR] ")" ) ] ")".

```
The VECTOR_SERIALIZE function is used to convert vector data or string data in vector form to VARCHAR or CLOB type data.

The VECTOR_SERIALIZE function has the same meaning as the [FROM_VECTOR](./FROM_VECTOR) function.