The UTL_ENCODE package provides functions for encoding RAW data types into standard encoding formats and corresponding decoding functions. The standard encoding format facilitates data transfer between servers. The above functions comply with published encoding standards.

This advanced package is not suitable for ISC Distributed Cluster Deployment.

## BASE64_ENCODE

```plsql
UTL_ENCODE.BASE64_ENCODE (R IN RAW) RETURN RAW;
```

The BASE64_ENCODE function encodes the input RAW data using BASE64 and outputs the encoded data.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| R             | The RAW string to be encoded.                           |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SELECT UTL_ENCODE.BASE64_ENCODE('0000') FROM DUAL;
```

## BASE64_DECODE

```plsql
UTL_ENCODE.BASE64_DECODE (R IN RAW) RETURN RAW;
```

The BASE64_DECODE function reads the BASE64 encoded input string and decodes it back to its original value.

|Parameter |Description |
| ------------ |----------------------|
| R             | The RAW string containing BASE64 encoded data. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SELECT UTL_ENCODE.BASE64_DECODE('4141413D') FROM DUAL;
```
