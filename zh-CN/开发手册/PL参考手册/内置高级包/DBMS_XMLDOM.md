XML DOM（XML Document Object Model）定义了所有XML元素的对象和属性，是用于访问XML文件完成获取、更改、添加或删除XML元素的标准。

DBMS_XMLDOM高级包提供了一组内置的存储过程、常量、数据结构，帮助用户对XML文档进行访问和处理。

## 预定义常量

DBMS_XMLDOM系统包定义了一组常量，供DBMS_XMLDOM高级包相关函数及子程序调用。下表为详细信息。

| 名称| 数据类型| 值| 描述|
| ----------- | ----------|-----------|-----------|
| ELEMENT_NODE                |INTEGER | 1   | 元素节点       |
| ATTRIBUTE_NODE              |INTEGER | 2   | 属性节点       |
| TEXT_NODE                   |INTEGER | 3   | 文本节点       |
| CDATA_SECTION_NODE          |INTEGER | 4   | 预留字段       |
| ENTITY_REFERENCE_NODE       |INTEGER | 5   | 预留字段       |
| ENTITY_NODE                 |INTEGER | 6   | 预留字段       |
| PROCESSING_INSTRUCTION_NODE |INTEGER | 7   | 预留字段       |
| COMMENT_NODE                |INTEGER | 8   | 注释节点        |
| DOCUMENT_NODE               |INTEGER | 9   | 文档节点       |
| DOCUMENT_TYPE_NODE          |INTEGER | 10  | 文档类型节点       |
| DOCUMENT_FRAGMENT_NODE      |INTEGER | 11  | 预留字段       |
| NOTATION_NODE               |INTEGER | 12  | 符号节点       |

## 数据结构

DBMS_XMLDOM系统包定义了一组数据结构，供DBMS_XMLDOM高级包相关函数及子程序调用。下表为详细信息。

| 名称| 数据类型| 描述|
| ------------- | ------- | ----------------------- |
| DOMDocument   |RECORD  | 实现了对XML文档对象的访问接口。         |
| DOMNode       |RECORD  | 实现了对XML文档对象中的节点的访问接口。           |
| DOMNodeList   |RECORD  | 实现了对XML文档对象中的节点列表的访问接口。          |
| DOMAttr       |RECORD  | 实现了对XML文档对象中的属性的访问接口。       |
| DOMElement    |RECORD  | 实现了对XML文档对象中的元素的访问接口。           |
| DOMText       |RECORD  | 实现了对XML文档对象中的文本的访问接口。           |


## 子程序说明

### appendChild Function

该函数用于为指定父节点追加子节点，追加的子节点将在末尾进行插入。

```plsql
DBMS_XMLDOM.appendChild(n IN DOMNode, newChild IN DOMNode) RETURN DOMNode;
```

|  参数| 用途|
| --- | --- |
| n      | 需要追加子节点的父节点    |
| newChild  | 待追加的节点    |

### createElement Functions

该函数用于创建一个新的元素节点。

```plsql
DBMS_XMLDOM.createElement(doc IN DOMDocument, tagName IN VARCHAR2) RETURN DOMElement;
```

```plsql
DBMS_XMLDOM.createElement(doc IN DOMDocument, tagName IN VARCHAR2, ns IN VARCHAR2) RETURN DOMElement;
```

|  参数| 用途|
| --- | --- |
| doc    | 待创建新元素节点的XML文档。如果指定的doc文档为null，则返回null；如果指定的doc已经被释放，则报错。   |
| tagName  | 设置新元素名称   |
| ns     | 设置新元素所属命名空间   |

**示例**

```plsql
DECLARE
  doc DBMS_XMLDOM.DOMDocument;
  root_node DBMS_XMLDOM.DOMElement;
BEGIN
  -- 创建XML文档DOM对象
  doc := DBMS_XMLDOM.newDOMDocument;

  -- 创建根节点，文档未释放时操作成功
  root_node := DBMS_XMLDOM.createElement(doc, 'Root'); 

  -- 释放文档
  DBMS_XMLDOM.freeDocument(doc); 

  -- 在已释放的文档中尝试再次创建节点将触发错误
  root_node := DBMS_XMLDOM.createElement(doc, 'NewRoot'); 
END;
/

YAS-07202 plugin execution error, invalid PL/SQL DOM handle
YAS-04015 at "SYS.DBMS_XMLDOM.CREATEELEMENT", line 78
YAS-04015 at line 19

```

### createTextNode Function

该函数用于创建一个新的文本节点。

```plsql
DBMS_XMLDOM.createTextNode(doc IN DOMDocument, data IN VARCHAR2) RETURN DOMText;
```

|  参数| 用途|
| --- | --- |
| doc    | 待创建文本节点的XML文档。如果指定的doc文档为null，则返回null；如果指定的doc已经被释放，则报错。   |
| data  | 待写入XML文档对象的文本内容   |


### freeDocument Procedure

该存储过程用于创建释放一个XML文档。

```plsql
DBMS_XMLDOM.freeDocument(doc IN DOMDocument);
```

|  参数| 用途|
| --- | --- |
| doc    | 待创建新元素的XML文档。如果指定的doc文档为null，则返回null。   |

### getAttribute Functions

该函数用于获取一个元素节点的属性值。如果查找的属性不存在，则返回NULL。

```plsql
DBMS_XMLDOM.getAttribute(elem IN DOMElement, name IN VARCHAR2) RETURN varchar2;
```

```plsql
DBMS_XMLDOM.getAttribute(elem IN DOMElement, name IN VARCHAR2, ns IN VARCHAR2) RETURN varchar2;
```

|  参数| 用途|
| --- | --- |
| elem    | 元素名称   |
| Name  | 属性名词   |
| ns     | 元素所属命名空间   |

### getChildNodes Function

该函数用于获取节点的所有子节点列表。

```plsql
DBMS_XMLDOM.getChildNodes(n IN DOMNode) RETURN DOMNodeList;
```

|  参数| 用途|
| --- | --- |
| n      | 元素节点名称    |


### getChildrenByTagName Functions

该函数用于获取元素的所有子节点。

```plsql
-- Returns children of the DOMELEMENT given the tag name
DBMS_XMLDOM.getChildrenByTagName
  (elem IN DOMElement,
   name varchar2) 
 RETURN DOMNodeList;
```

```plsql
-- Returns children of the DOMELEMENT given the tag name and namespace
DBMS_XMLDOM.getChildrenByTagName
  (elem IN DOMElement,
   name varchar2,
    ns varchar2)
 RETURN DOMNodeList;
```

|  参数| 用途|
| --- | --- |
| elem    | 元素名称   |
| Name  | 属性名称   |
| ns     | 元素所属命名空间   |

### getDocumentElement Function

该函数用于获取XML文档的根元素。

```plsql
DBMS_XMLDOM.getDocumentElement(doc IN DOMDocument) RETURN DOMElement;
```

|  参数| 用途|
| --- | --- |
| doc    | XML DOMDocument文档。如果指定的doc文档为null，则返回null；如果指定的doc已经被释放，则报错。   |

### getElementsByTagName Functions

该函数用于获取指定节点的所有子节点列表。

如果查找的对象为空（DOMElement或DOMDocument），或者没有指定节点名称，则函数返回NULL。

```plsql
DBMS_XMLDOM.getElementsByTagName
  (elem IN DOMElement, 
   name IN VARCHAR2) 
 RETURN DOMNodeList;
```
```plsql
DBMS_XMLDOM.getElementsByTagName
  (elem IN DOMElement,
   name IN VARCHAR2,
   ns varchar2) 
 RETURN DOMNodeList;
```
```plsql
DBMS_XMLDOM.getElementsByTagName
  (doc IN DOMDocument,
   tagname IN VARCHAR2) 
 RETURN DOMNodeList;
```

|  参数| 用途|
| --- | --- |
| elem    | 元素名称   |
| Name  | 指定需要查找的节点名称   |
| ns     | 指定需要查找的元素节点所属命名空间   |
| doc    | 指定需要查找的XML DOMDocument文档。   |
| tagName  | 指定需要查找的元素节点的tag   |

### getFirstChild Function

该函数用于获取指定节点的第一个子节点。

```plsql
DBMS_XMLDOM.getFirstChild(n IN DOMNode) RETURN DOMNode;
```

|  参数| 用途|
| --- | --- |
| n      | 元素节点名称    |

### getLength Function

该函数用于从节点列表中获取节点数量，需要依赖其他函数先获取节点列表。

```plsql
DBMS_XMLDOM.getLength(nl IN DOMNodeList) RETURN INTEGER;
```

|  参数| 用途|
| --- | --- |
| nl      | 节点列表    |

### getNodeName Function

该函数用于获取节点名称。

```plsql
DBMS_XMLDOM.getNodeName(n IN DOMNode) RETURN VARCHAR2;
```

|  参数| 用途|
| --- | --- |
| n      | 节点名称    |

### getNodeValue Function

该函数用于获取节点数据。

```plsql
DBMS_XMLDOM.getNodeValue(n IN DOMNode) RETURN VARCHAR2;
```

|  参数| 用途|
| --- | --- |
| n      | 节点名称    |

### getParentNode Function

该函数用于获取父节点。

```plsql
DBMS_XMLDOM.getParentNode(n IN DOMNode) RETURN DOMNode;
```

|  参数| 用途|
| --- | --- |
| n      | 节点名称    |

### getValue Function

该函数用于获取属性值。

```plsql
DBMS_XMLDOM.getValue (a IN DOMAttr) RETURN VARCHAR2;
```

|  参数| 用途|
| --- | --- |
| a      | 属性名称    |

### isNull Function

该函数用于确认XML文档是否为空，为空时返回TRUE，反之返回FALSE。

```plsql
DBMS_XMLDOM.isNull(doc IN DOMDocument) RETURN BOOLEAN;
```

|  参数| 用途|
| --- | --- |
| doc    | 指定需要查找的XML DOMDocument文档   |

### item Function

该函数用于获取指定位置的节点名称。

```plsql
DBMS_XMLDOM.item(nl IN DOMNodeList, idx IN INTEGER) RETURN DOMNode;
```

|  参数| 用途|
| --- | --- |
| nl      | 指定的DOMNodeList。如果输入的DOMNodeList为NULL，则函数返回NULL    |
| idx     | 指定的节点位置。<br>- 输入NULL时，返回第一个节点。<br>- 如果指定的位置超出DOMNodeList长度，则函数返回NULL。      |

### makeElement Function

该函数用于将指定节点转换为元素节点。

```plsql
DBMS_XMLDOM.makeElement(n IN DOMNode) RETURN DOMElement;
```

|  参数| 用途|
| --- | --- |
| n      | 节点名称    |

### makeNode Functions

这组函数用于将XML文档、文本、属性、元素转换为XML文档的节点对象。

```plsql
DBMS_XMLDOM.makeNode(doc IN DOMDocument) RETURN DOMNode;
```

```plsql
DBMS_XMLDOM.makeNode(t IN DOMText) RETURN DOMNode;
```

```plsql
DBMS_XMLDOM.makeNode(a IN DOMAttr) RETURN DOMNode;
```

```plsql
DBMS_XMLDOM.makeNode(elem IN DOMElement) RETURN DOMNode;
```

|  参数| 用途|
| --- | --- |
| doc    | 指定XML DOMDocument文档   |
| t      | 指定DOMText    |
| a      | 指定DOMAttr    |
| elem    | 指定DOMElement   |

**示例**

```plsql
DECLARE
  doc DBMS_XMLDOM.DOMDocument;
  book_element DBMS_XMLDOM.DOMElement;
  new_node DBMS_XMLDOM.DOMNode;
BEGIN
  doc := DBMS_XMLDOM.newDOMDocument();

  book_element := DBMS_XMLDOM.createElement(doc, 'book');
  
  -- 将元素附加到文档根节点
  new_node := DBMS_XMLDOM.appendChild(DBMS_XMLDOM.makeNode(doc), DBMS_XMLDOM.makeNode(book_element));
END;
/

PL/SQL Succeed.
```

### newDomDocument Function

该组函数用于创建一个新的XML文档对象。

创建一个空的DOMDocument。

```plsql
DBMS_XMLDOM.newDomDocument
  RETURN DOMDOCUMENT;
```

根据输入的xmltype类型文件创建一个DOMDocument。

```plsql
DBMS_XMLDOM.newDomDocument
 (
    xmldoc IN sys.xmltype)
  RETURN DOMDOCUMENT;
```

根据输入的clob数据创建一个DOMDocument。

```plsql
DBMS_XMLDOM.newDomDocument
 (
    c IN clob)
  RETURN DOMDOCUMENT;
```

|  参数| 用途|
| --- | --- |
| xmldoc | 输入类型为XMLType的文件 |
| c      | 输入格式为clob类型的数据      |


### setAttribute Procedures

该组存储过程用于为元素属性设置属性值。

```plsql
DBMS_XMLDOM.setAttribute
  (elem IN DOMElement,
   name IN VARCHAR2,
   newvalue IN VARCHAR2);
```

```plsql
DBMS_XMLDOM.setAttribute
  (elem IN DOMElement,
   name IN VARCHAR2,
   newvalue IN VARCHAR2,
   ns IN VARCHAR2);
```

|  参数| 用途|
| --- | --- |
| elem    | 指定DOMElement   |
| Name    | 指定属性名称   |
| newvalue    | 配置属性值   |
| ns     | 指定需要查找的元素节点所属命名空间   |

### setNodeValue Procedure

该函数用于设置节点值。

```plsql
DBMS_XMLDOM.setNodeValue(n IN DOMNode, nodeValue IN VARCHAR2);
```

|  参数| 用途|
| --- | --- |
| n    | 指定DOMNode   |
| nodeValue    | 配置节点值   |

### writeToBuffer Procedures

该组存储过程用于将XML文档对象全文或指定节点写入缓冲区。

```plsql
DBMS_XMLDOM.writeToBuffer(n IN DOMNode, buffer IN OUT VARCHAR2);
```

```plsql
DBMS_XMLDOM.writeToBuffer(doc IN DOMDocument, buffer IN OUT VARCHAR2);
```

|  参数| 用途|
| --- | --- |
| n    | 指定DOMNode   |
| doc    | 待处理的DOMDocument对象   |
| buffer | 待写入的缓存区域  |


**示例**

```plsql
DECLARE
  doc DBMS_XMLDOM.DOMDocument;
  buffer CLOB;
  new_node DBMS_XMLDOM.DOMNode;
BEGIN
  -- 创建 XML 文档
  doc := DBMS_XMLDOM.newDOMDocument;
  
  -- 创建根元素并附加内容
  new_node := 
  DBMS_XMLDOM.appendChild(
    DBMS_XMLDOM.makeNode(doc), 
    DBMS_XMLDOM.makeNode(DBMS_XMLDOM.createElement(doc, 'Root'))
  );
  
  -- 将 XML 文档写入缓冲区
  DBMS_XMLDOM.writeToBuffer(doc, buffer);
  
  -- 输出缓冲区大小
  DBMS_OUTPUT.PUT_LINE('Buffer Size: ' || DBMS_LOB.getLength(buffer) || ' bytes');
  
  DBMS_XMLDOM.freeDocument(doc);
END;
/

--result

Buffer Size: 38 bytes

PL/SQL Succeed.
```

## 示例

创建一个空文档，在元素节点下，多次添加元素节点且key值一样。

```plsql
DECLARE
   doc DBms_xmldom.domdocument;
   elem2 DBms_xmldom.domelement;
   docnode DBms_XMLDOM.DOMNode;
   buffer clob;
   value  varchar(1000);
begin
   doc := DBms_xmldom.newDOMDocument('
            <items>
                <item id="1">Apple</item>
                <item id="2">Banana</item>
                <item id="3"></item>
            </items>
        ');
   for i in 0..10 loop
      elem2 := DBms_XMLDOM.CREATEELEMENT(doc, 'item');
      DBms_XMLDOM.setattribute(elem2, 'id', '3');
      docnode := DBms_XMLDOM.appendChild(xmldom.getFirstChild(DBms_XMLDOM.makeNode(doc)), DBms_XMLDOM.makeNode(elem2));
   end loop;
   DBms_xmldom.writetobuffer(doc, buffer);
   dbms_output.put_line('buffer: ');
   dbms_output.put_line(buffer);
   xmldom.freedocument(doc);
END;
/

--result

buffer: 
<?xml version=\"1.0\" encoding=\"UTF8\" standalone=\"no\"?>\n<items><item id=\"1\">Apple</item><item id=\"2\">Banana</item><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/><item id=\"3\"/></items>

PL/SQL Succeed.
```
