## 功能简介

返回诊断记录的多个字段的当前值，包括SQLSTATE、本机错误代码和诊断消息文本。

## 函数声明

```c
SQLRETURN SQLGetDiagRec(  
     SQLSMALLINT     HandleType,  
     SQLHANDLE       Handle,  
     SQLSMALLINT     RecNumber,  
     SQLCHAR *       SQLState,  
     SQLINTEGER *    NativeErrorPtr,  
     SQLCHAR *       MessageText,  
     SQLSMALLINT     BufferLength,  
     SQLSMALLINT *   TextLengthPtr);
```

## 参数说明

|  参数名| 说明|
| -------------------- | ------------------------------------------------------------ |
| HandleType (IN)      | 描述需要诊断的句柄类型的句柄类型标识符。                     |
| Handle (IN)          | 诊断数据结构的句柄，由 HandleType 指示的类型。               |
| RecNumber (IN)       | 指示应用程序从中查找信息的状态记录。                         |
| SQLState (OUT)       | 指向缓冲区的指针，该缓冲区将返回一个五个字符的 SQLSTATE 代码。 |
| NativeErrorPtr (OUT) | 指向缓冲区的指针，该缓冲区将返回特定于数据源的本机错误代码。 |
| MessageText (OUT)    | 指向要在其中返回诊断消息文本字符串的缓冲区的指针。           |
| BufferLength (IN)    | *MessageText 缓冲区的长度（以字符为单位）。                  |
| TextLengthPtr (OUT)  | 指向缓冲区的指针，该缓冲区返回的总字符数。                   |
