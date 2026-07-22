DBMS_XSLPROCESSOR高级包提供了管理XML文档内容和结构的接口。

## 子程序说明

### selectNodes

selectnodes函数从​​DOM树中选择匹配给定路径表达式的节点，并返回选择结果。

```plsql
FUNCTION selectNodes(n IN DBMS_XMLDOM.DOMNode, pattern IN VARCHAR2, namespace IN VARCHAR2 := NULL) RETURN DBMS_XMLDOM.DOMNodeList;
```

|  参数| 用途|
| --- | --- |
| n    | 指定根节点DOMNode。如果该参数为null，则返回null。   |
| pattern | 指定检索路径格式。该参数不可为null，否则报错。 |
| namespace | 声明命名空间。如果pattern中指定了命名空间，此处配置需包含pattern中的命名空间；如果pattern中未指定命名空间，此参数无需传参。|

### selectSingleNode

selectsinglenode函数用于从​​DOM树中查找第一个满足给定路径表达式的节点。

```plsql
FUNCTION selectSingleNode(n IN DBMS_XMLDOM.DOMNode, pattern IN varchar2, namespace IN VARCHAR2 := NULL) return DBMS_XMLDOM.DOMNode;
```

|  参数| 用途|
| --- | --- |
| n    | 指定根节点DOMNode。如果该参数为null，则返回null。   |
| pattern | 指定检索路径格式。该参数不可为null，否则报错。 |
| namespace | 声明命名空间。如果pattern中指定了命名空间，此处配置需包含pattern中的命名空间；如果pattern中未指定命名空间，此参数无需传参。|

### valueOf Function/Procedure

valueof函数和存储过程用于在​​DOM树中检索满足指定模式匹配的第一个节点的值。

```plsql
PROCEDURE valueOf(n IN DBMS_XMLDOM.DOMNode, pattern IN VARCHAR2, val OUT VARCHAR2, namespace IN VARCHAR2 := NULL);

FUNCTION valueOf(n IN DBMS_XMLDOM.DOMNode, pattern IN varchar2, namespace IN VARCHAR2 := NULL) return VARCHAR2;
```

|  参数| 用途|
| --- | --- |
| n    | 指定根节点DOMNode。如果该参数为null，则返回null。   |
| pattern | 指定检索路径格式。该参数不可为null，否则报错。 |
| namespace | 声明命名空间。如果pattern中指定了命名空间，此处配置需包含pattern中的命名空间；如果pattern中未指定命名空间，此参数无需传参。|
| val | 返回匹配的结果 |


## 示例

示例一：selectNodes和selectSingleNode的使用方法

```plsql
set serveroutput on;
DECLARE
  xml_data XMLType := XMLType('
    <ns:library xmlns:ns="http://example.com/library">
      <ns:books>
        <ns:book id="1" category="programming">
          <ns:title>YashanDB Advanced Programming</ns:title>
          <ns:author>Nicholas</ns:author>
          <ns:price currency="USD">55.99</ns:price>
        </ns:book>
        <ns:book id="2" category="database">
          <ns:title>SQL Mastery</ns:title>
          <ns:author>Jane</ns:author>
          <ns:price currency="USD">45.99</ns:price>
        </ns:book>
      </ns:books>
    </ns:library>
  ');
    dom_doc DBMS_XMLDOM.DOMDocument;
    context_node DBMS_XMLDOM.DOMNode;
    single_node DBMS_XMLDOM.DOMNode;
    node_list DBMS_XMLDOM.DOMNodeList;
    node_value VARCHAR2(1000);
    ns_map VARCHAR2(100);
    current_node DBMS_XMLDOM.DOMNode;
BEGIN
    dom_doc := DBMS_XMLDOM.NEWDOMDOCUMENT(xml_data);
    context_node := DBMS_XMLDOM.MAKENODE(DBMS_XMLDOM.GETDOCUMENTELEMENT(dom_doc));
    ns_map := 'xmlns:ns="http://example.com/library"';
    node_list := DBMS_XSLPROCESSOR.SELECTNODES(
      n => context_node,
      pattern => 'ns:books/ns:book',
      namespace => ns_map
    );
    DBMS_OUTPUT.PUT_LINE('Find ' || DBMS_XMLDOM.GETLENGTH(node_list) || ' book nodes');
    FOR i IN 0..DBMS_XMLDOM.GETLENGTH(node_list)-1 LOOP
        current_node := DBMS_XMLDOM.ITEM(node_list, i);
        single_node := DBMS_XSLPROCESSOR.SELECTSINGLENODE(current_node, 'ns:title',ns_map);
        IF single_node.id is not null THEN
            node_value := DBMS_XMLDOM.GETNODEVALUE(DBMS_XMLDOM.GETFIRSTCHILD(single_node));
            DBMS_OUTPUT.PUT_LINE('Book ' || (i+1) || ': ' || node_value);
        ELSIF single_node.id is null then 
            dbms_output.put_line('return null');
        END IF;
    END LOOP;
    DBMS_XMLDOM.FREEDOCUMENT(dom_doc);
END;
/

--result
Find 2 book nodes
Book 1: YashanDB Advanced Programming
Book 2: SQL Mastery

PL/SQL Succeed.
```

示例二：valueOf的使用方法

```plsql
set serveroutput on;
DECLARE
  xml_doc    DBMS_XMLDOM.DOMDOCUMENT;
  root_node  DBMS_XMLDOM.DOMNODE;
  result     VARCHAR2(200);
BEGIN
  xml_doc := DBMS_XMLDOM.NEWDOMDOCUMENT(
    XMLTYPE('<catalog>
               <book id="B001">
                 <title>YashanDB Coding</title>
                 <author>zhangsan</author>
                 <price>89.00</price>
               </book>
             </catalog>')
  );

  root_node := DBMS_XMLDOM.MAKENODE(
    DBMS_XMLDOM.GETDOCUMENTELEMENT(xml_doc)
  );

  result := DBMS_XSLPROCESSOR.VALUEOF(
    n => root_node,
    pattern => 'book/price'
  );

  DBMS_OUTPUT.PUT_LINE('Price: ' || result);

  DBMS_XMLDOM.FREEDOCUMENT(xml_doc);
END;
/

--result
Price: 89.00

PL/SQL Succeed.
```