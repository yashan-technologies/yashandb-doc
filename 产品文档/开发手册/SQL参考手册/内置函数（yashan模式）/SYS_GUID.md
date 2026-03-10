```ebnf+diagram
sys_guid::= SYS_GUID "("")"
```

SYS_GUID函数返回一个16字节的全库唯一数，返回类型为RAW类型。
> **Note**:
>
> 修改系统时间，可能导致生成的唯一ID不唯一。
> 
示例

```sql
SELECT SYS_GUID() res FROM DUAL;

RES                        
--------------------------------- 
01016C6484F5932806001A0001000000 

```

