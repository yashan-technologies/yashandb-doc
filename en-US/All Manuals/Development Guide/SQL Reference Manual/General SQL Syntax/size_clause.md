**size_clause::=**

```ebnf+diagram
syntax::= integer [B|K|M|G|T|P|E]
```

The size_clause is used to define storage capacity units. This statement may be used in some DDL statements to describe physical storage properties. YashanDB supports the following unit values for defining storage capacity:

- B: bytes
- K: kilobytes  
- M: megabytes 
- G: gigabytes 
- T: terabytes
- P: petabytes
- E: exabytes
