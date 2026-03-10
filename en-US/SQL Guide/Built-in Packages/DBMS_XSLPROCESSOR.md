The DBMS_XSLPROCESSOR advanced package provide a data interface to manage the contents and the structure of XML documents.

## Subprograms Summary of DBMS\_XMLDOM

### selectNodes

The selectnodes function selects nodes that match the given path expression from the DOM tree and returns the selection results.

```plsql
FUNCTION selectNodes(n IN DBMS_XMLDOM.DOMNode, pattern IN VARCHAR2, namespace IN VARCHAR2 := NULL) RETURN DBMS_XMLDOM.DOMNodeList;
```

|Parameter |Purpose |
| --- | --- |
| n    | Specify the DOMNode; Specify the DOM document object DOMDocument. If the specified doc document is null, then null is returned; if the specified doc has been released, an error is reported.  |
| pattern | Specify retrieval path format. If the specified pattern is null, an error is reported.  |
| namespace | Declare namespace. If namespace is specified in pattern, this configuration must include the namespace in pattern; if no namespace is specified in pattern, this parameter does not need to be set. |

### selectSingleNode

The selectsinglenode function is used to find the first node that satisfies the given path expression from the DOM tree.

```plsql
FUNCTION selectSingleNode(n IN DBMS_XMLDOM.DOMNode, pattern IN varchar2, namespace IN VARCHAR2 := NULL) return DBMS_XMLDOM.DOMNode;
```

|Parameter |Purpose |
| --- | --- |
| n    | Specify the DOMNode; Specify the DOM document object DOMDocument. If the specified doc document is null, then null is returned; if the specified doc has been released, an error is reported.  |
| pattern | Specify retrieval path format. If the specified pattern is null, an error is reported.  |
| namespace | Declare namespace. If namespace is specified in pattern, this configuration must include the namespace in pattern; if no namespace is specified in pattern, this parameter does not need to be set. |

### valueOf Function/Procedure

The valueof function and stored procedure are used to retrieve the value of the first node that satisfies the specified pattern match in the DOM tree.

```plsql
PROCEDURE valueOf(n IN DBMS_XMLDOM.DOMNode, pattern IN VARCHAR2, val OUT VARCHAR2, namespace IN VARCHAR2 := NULL);

FUNCTION valueOf(n IN DBMS_XMLDOM.DOMNode, pattern IN varchar2, namespace IN VARCHAR2 := NULL) return VARCHAR2;
```

|Parameter |Purpose |
| --- | --- |
| n    | Specify the DOMNode; Specify the DOM document object DOMDocument. If the specified doc document is null, then null is returned; if the specified doc has been released, an error is reported.  |
| pattern | Specify retrieval path format. If the specified pattern is null, an error is reported.  |
| namespace | Declare namespace. If namespace is specified in pattern, this configuration must include the namespace in pattern; if no namespace is specified in pattern, this parameter does not need to be set. |
| val | Return the matching results |


## Example 

Example 1: Use method for selectNodes and selectSingleNode

```plsql
SET serveroutput ON;
DECLARE
  xml_data XMLType := XMLType('
    <ns:LIBRARY xmlns:ns="http://example.com/library">
      <ns:books>
        <ns:book id="1" CATEGORY="programming">
          <ns:title>YashanDB Advanced Programming</ns:title>
          <ns:author>Nicholas</ns:author>
          <ns:price currency="USD">55.99</ns:price>
        </ns:book>
        <ns:book id="2" CATEGORY="database">
          <ns:title>SQL Mastery</ns:title>
          <ns:author>Jane</ns:author>
          <ns:price currency="USD">45.99</ns:price>
        </ns:book>
      </ns:books>
    </ns:LIBRARY>
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
        IF single_node.id IS NOT null THEN
            node_value := DBMS_XMLDOM.GETNODEVALUE(DBMS_XMLDOM.GETFIRSTCHILD(single_node));
            DBMS_OUTPUT.PUT_LINE('Book ' || (i+1) || ': ' || node_value);
        ELSIF single_node.id IS null THEN 
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

```

Example 2: Use method for valueOf

```plsql
SET serveroutput ON;
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

```