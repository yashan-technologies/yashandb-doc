UTL_I18N包为一组服务，用于将字符串按照指定字符集进行转换，根据全球不同地区间字符显示的差异进行区域化处理。

## STRING_TO_RAW

```plsql
UTL_I18N.STRING_TO_RAW( 
 data IN VARCHAR2 CHARACTER SET ANY_CS,
 dst_charset IN VARCHAR2 DEFAULT NULL)
RETURN RAW;
```

该函数将VARCHAR2或NVARCHAR2字符串转换为另一个字符集，结果作为RAW类型返回。

|  参数| 描述|
| ----------- | ---------------------------------------------- |
| data        | 待转换的VARCHAR2或NVARCHAR2字符串。            |
| dst_charset | 目标字符集，若为空对于char类型，默认字符集是数据库字符集；对于nchar类型，默认字符集是国家字符集。 |
注：如果用户指定了无效的字符集或者空字符串，则该函数将返回空字符串。
示例

```plsql
SELECT utl_i18n.string_to_raw('abcdef', 'utf8') FROM dual;

UTL_I18N.STRING_TO_R                                             
---------------------------------------------------------------- 
616263646566                                                    

1 row fetched.
```

## RAW_TO_CHAR

```plsql
UTL_I18N.RAW_TO_CHAR(
 data IN RAW,
 src_charset IN VARCHAR2 DEFAULT NULL)
 RETURN VARCHAR2;
```

该函数将未在数据库字符集中编码的RAW数据转换为VARCHAR2字符串。

|  参数| 描述|
| ----------- |----------------------------|
| data        | 待转换的RAW类型数据。               |
| src_charset | 指定导出RAW数据的字符集。若为空，CHAR类型默认使用数据库字符集，NCHAR类型默认使用国家字符集。 |

如果指定了无效的字符集或空字符串，该函数将返回空字符串。

示例

```plsql
SELECT utl_i18n.raw_to_char('616263646566', 'utf8') FROM dual;

UTL_I18N.RAW_TO_CHAR                                             
---------------------------------------------------------------- 
abcdef                                                          

1 row fetched.
```

## 异常说明

|  参数| 描述|
| ----------------------- | ---------------------------------- |
| dst_charset/src_charset | 若为yashan不支持的无效字符集，则返回NULL |
| data | 若不满足字符集要求的异常字符串，则报错字符集不匹配 |

示例

```plsql
select UTL_I18N.STRING_TO_RAW('hello', 'JA16SJIS') from dual;

UTL_I18N.STRING_TO_R                                             
---------------------------------------------------------------- 

1 row fetched.

select UTL_I18N.RAW_TO_CHAR('8137A832', 'utf8') from dual;

YAS-00228 character set mismatch
```
