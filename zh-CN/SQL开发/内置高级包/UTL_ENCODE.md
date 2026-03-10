UTL_ENCODE包提供将RAW类型数据编码为标准编码格式的函数以及对应的解码函数，标准编码格式更便于数据在服务器之间传输。上述函数均遵循已发布的编码标准。

该高级包不适用于存算一体分布式集群部署。

## BASE64\_ENCODE

```PLSQL
UTL_ENCODE.BASE64_ENCODE (R IN RAW) RETURN RAW;
```

BASE64_ENCODE函数采用BASE64将输入的RAW类型数据进行编码并输出编码后的数据。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| R     | 要编码的RAW字符串。                                              |

示例（单机/共享集群/分布式集群部署）

```plsql
SELECT UTL_ENCODE.BASE64_ENCODE('0000') FROM DUAL;
```

## BASE64\_DECODE

```PLSQL
UTL_ENCODE.BASE64_DECODE (R IN RAW) RETURN RAW;
```

BASE64_DECODE函数用于读取BASE64编码的原始输入字符串，并将其解码为原始值。

|  参数| 描述|
| ------------ |----------------------|
| R     | 包含BASE64编码数据的RAW字符串。 |

示例（单机/共享集群/分布式集群部署）

```plsql
SELECT UTL_ENCODE.BASE64_DECODE('4141413D') FROM DUAL;
```
