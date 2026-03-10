
**size_clause::=**

```ebnf+diagram
syntax::= integer [B|K|M|G|T|P|E]
```

size_clause用于定义存储容量单位，该语句在一些DDL语句中描述物理存储属性时可能被使用。YashanDB支持使用如下单位值进行存储容量的定义：

- B: bytes
- K: kilobytes  
- M: megabytes 
- G: gigabytes 
- T: terabytes
- P: petabytes
- E: exabytes
