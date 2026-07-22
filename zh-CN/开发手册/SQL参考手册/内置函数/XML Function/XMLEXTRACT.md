```ebnf
xmlextract = XMLEXTRACT "("xml_data "," xpath [ "," namespace] ")".
```

XMLEXTRACT函数根据输入的xpath和namespace对xml_data提取数据，返回一个包含XML片段的XMLTYPE实例。



**xml_data**

[通用表达式](../../通用SQL语法/expr)，遵循如下规则：

- 必须是一个完整的带有根节点的XML文档，数据类型为XMLTYPE。

- 数据长度不得超过64MB。

- 当xml_data的值为NULL时，函数返回NULL。




**xpath**

XML路径表达式，应遵循W3C（World Wide Web Consortium） XPath 1.0规范。

- 类型必须为字符型，其他类型则报错。

- xpath的最大Size为65534字节。

- 当xpath的值为NULL，函数将报错。




**namespace**

用于指定命名空间，可以有多个，不同命名空间之间可以使用逗号、空格、制表符或换行符进行分割。命名空间格式为`xmlns:prefix=href`，区分大小写，可缺省prefix，此时为默认命名空间。

- 遵循W3C(World Wide Web Consortium)的XML命名空间规范。

- 类型必须为字符型，可以缺省，缺省时默认为NULL。

- namespace的最大Size为65534字节。

- 若XML数据中存在命名空间无前缀的情况，YashanDB会自动为其添加缺省前缀`syspfx`，在指定xpath时需显式指定缺省前缀`syspfx`。


示例（HEAP表）

```sql
-- 查询root节点下的内容
select XMLExtract(XMLParse(DOCUMENT '<root><role1>Jerry</role1>Tom<role2></role2></root>'), '/root').GetClobVal() res from dual;

RES
----------------------------------------------------------------
<root><role1>Jerry</role1>Tom<role2/></root>

-- xml_data输入为NULL，查询结果为NULL
select XMLExtract(NULL, '/').GetClobVal() res from dual;

RES
----------------------------------------------------------------

    
-- xpath不能为NULL
select XMLExtract(NULL, NULL).GetClobVal() res from dual;

YAS-07202 plugin execution error, Invalid XPATH expression

-- 使用命名空间查询
select XMLExtract(XMLTYPE('<root xmlns:ns="http://example.com/namespace" xmlns:ns1="http://example.com/namespace1"><element>Content1</element><ns:element>Content2</ns:element></root>'),
                    '/root/element', 'xmlns:ns="http://example.com/namespace"').GetClobVal() res from dual;

RES
----------------------------------------------------------------
<element>Content1</element>

-- 显式指定命名空间前缀syspfx
select XMLEXTRACT(XMLTYPE('<a xmlns="ns1" xmlns:f="ns2"><f:b b1="b1" b2="b2">bbb1</f:b><b b1="b1" b2="b2">bbb2</b></a>'), 'syspfx:a/f:b', 'xmlns="ns1" xmlns:f="ns2"').getstringval() from dual;

XMLEXTRACT(XMLTYPE('<AXMLNS="NS1"XMLNS:F="NS2"><F:BB1="B1"B2="B2
----------------------------------------------------------------
<f:b xmlns:f="ns2" b1="b1" b2="b2">bbb1</f:b>
```
