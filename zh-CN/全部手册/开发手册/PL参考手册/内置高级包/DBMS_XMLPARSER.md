DBMS_XMLPARSER高级包包含Parser类型数据接口和一组内置的子程序，主要用于对XML文档的解析、验证和转换。



当前DBMS_XMLPARSER高级包应用需遵循如下用法：

- YashanDB已预置同义词XMLPARSER，使用时效果与DBMS_XMLPARSER高级包完全相同。

- 对需要处理的XML文件要求必须是UTF8字符编码。

- 访问指定路径的XML文件时，需保证文件位于数据库的安全目录`SECURE_FILE_PRIV`中。请以拥有系统管理权限ALTER SYSTEM的用户参考如下步骤配置数据库安全目录。

  ```sql
  ALTER SYSTEM SET secure_file_priv='/data/tmp' scope=memory;
  
  SHOW PARAMETER SECURE_FILE_PRIV;

  NAME                                                             VALUE                                                  
  ---------------------------------------------------------------- ----------------------------------------------------------------
  SECURE_FILE_PRIV                                                 /data/tmp                                              

  ```

- DBMS_XMLPARSER.PARSE函数暂不支持访问网络地址。

- 对需要处理的XML文件大小要求不超过64MB。

- 暂不支持XML文件内外部引用DTD。

- DBMS_XMLPARSER高级包暂不支持并发执行。

## 数据结构

DBMS_XMLPARSER定义了数据结构Parser，供相关函数及子程序调用。

| 名称| 数据类型| 描述|
| ------------- | ------- | ----------------------- |
| Parser   |RECORD  | 实现了XML文档对象的访问接口。         |

## 子程序说明

### freeParser Procedure

该过程用于释放XML文件解析实例。

```plsql
XMLPARSER.freeParser(n IN Parser);
```

|  参数| 用途|
| --- | --- |
| n    | 需释放的Parser解析实例   |

### getDocument Function

该函数用于获取Parser实例构建的​​DOM文档对象​DOMDocument，函数要求必须在成功调用parse函数或子过程后使用，否则返回NULL。


```plsql
XMLPARSER.getDocument(p Parser) RETURN DBMS_XMLDOM.DOMDocument;
```

|  参数| 用途|
| --- | --- |
| p    | 指定Parser实例    |

### newParser Function

创建并初始化XML文件解析实例。

```plsql
XMLPARSER.newParser() RETURN Parser;
```

**示例**

```plsql
DECLARE
  parser XMLPARSER.PARSER;
  doc XMLDOM.DOMDOCUMENT;
  node XMLDOM.DOMNODE;
  value xmltype;
BEGIN
  parser := XMLPARSER.NEWPARSER();
  XMLPARSER.freeparser(parser);
END;
/

```

### parse Function/Procedure

XML解析函数及存储过程，用于解析存储在给定URL或文件中的XML文件。

```plsql
Function XMLPARSER.parse(url IN VARCHAR2) RETURN DBMS_XMLDOM.DOMDocument;

Procedure XMLPARSER.parse(p Parser, url IN VARCHAR2);
```

|  参数| 用途|
| --- | --- |
| p    | 指定Parser实例    |
| url  | 指定XML文件位置。要求必须是​​绝对路径​​或可访问的URL，且必须存在数据库安全目录中 |


### parseBuffer Procedure

XML解析程序，用于解析字符串格式的XML数据。

```plsql
XMLPARSER.parseBuffer(p Parser, doc IN VARCHAR2);
```

|  参数| 用途|
| --- | --- |
| p    | 指定Parser实例    |
| doc  | 输入字符串格式的XML数据，要求字符串​​最大长度不超过32767字符​​  |


### parseClob Procedure

XML解析程序，用于解析CLOB格式的XML数据。

```plsql
XMLPARSER.parseClob(p Parser, doc IN CLOB);
```

|  参数| 用途|
| --- | --- |
| p    | 指定Parser实例    |
| doc  | 输入CLOB格式的XML数据，要求CLOB内容需为​​格式完整的XML数据|

## 示例

示例一：解析XML数据

```plsql
DECLARE
  parser XMLPARSER.PARSER;
  doc XMLDOM.DOMDOCUMENT;
  node XMLDOM.DOMNODE;
  curr_node DBMS_XMLDOM.DOMNODE;
  node_list DBMS_XMLDOM.DOMNODELIST;
  element DBMS_XMLDOM.domelement;
  elem DBMS_XMLDOM.domelement;
  value CLOB;
  a INT;
BEGIN
parser := XMLPARSER.NEWPARSER();
  value := '<?xml version="1.0"?>
<Employees>
  <Employee id="101">
    <Name>Alice</Name>
    <Dept>IT</Dept>
  </Employee>
  <Employee id="102">
     <Name>Bob</Name>
     <Dept>HR</Dept>
   </Employee>
</Employees>';
  
  -- Parse the XML file
  XMLPARSER.PARSEBUFFER(parser, value);
  doc := XMLPARSER.GETDOCUMENT(parser);
  
  element := DBMS_XMLDOM.GETDOCUMENTELEMENT(doc);
  node_list := DBMS_XMLDOM.getelementsbytagname(element, 'Employee');

  FOR i IN 0..DBMS_XMLDOM.getlength(node_list) -1 LOOP
    curr_node := DBMS_XMLDOM.item(node_list, i);
    elem := DBMS_XMLDOM.makeElement(curr_node);
    
    -- get the value
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || 
        DBMS_XMLDOM.getAttribute(elem, 'id'));
    
    
    DBMS_OUTPUT.PUT_LINE('-----');
  END LOOP;
  XMLPARSER.FREEPARSER(parser);

END;
/

--result

Employee ID: 101
-----
Employee ID: 102
-----

```

示例二：解析XML文件

```plsql
DECLARE
  parser XMLPARSER.PARSER;
  doc XMLDOM.DOMDOCUMENT;
  node XMLDOM.DOMNODE;
  value xmltype;
  curr_node DBMS_XMLDOM.DOMNODE;
  node_list DBMS_XMLDOM.DOMNODELIST;
  element DBMS_XMLDOM.domelement;
  elem DBMS_XMLDOM.domelement;
BEGIN
  parser := XMLPARSER.NEWPARSER();
  XMLPARSER.parse(parser, 'SECURE_FILE_PRIV/sample.xml');
  
  doc := XMLPARSER.GETDOCUMENT(parser);

  element := DBMS_XMLDOM.GETDOCUMENTELEMENT(doc);
  node_list := DBMS_XMLDOM.getelementsbytagname(element, 'Employee');

  FOR i IN 0..DBMS_XMLDOM.getlength(node_list) -1 LOOP
    curr_node := DBMS_XMLDOM.item(node_list, i);
    elem := DBMS_XMLDOM.makeElement(curr_node);
    
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || 
        DBMS_XMLDOM.getAttribute(elem, 'id'));
    
    
    
    DBMS_OUTPUT.PUT_LINE('-----');
  END LOOP;
  XMLPARSER.FREEPARSER(parser);
END;
/

--result

Employee ID: 101
-----
Employee ID: 102
-----

```