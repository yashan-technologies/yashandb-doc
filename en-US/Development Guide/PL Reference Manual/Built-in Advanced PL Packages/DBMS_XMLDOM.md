XML DOM (XML Document Object Model) defines objects and properties for all XML elements, and is a standard for accessing XML files to retrieve, modify, add or delete XML elements.

The DBMS_XMLDOM advanced package provides a set of built-in stored procedures, constants, and data structures to help users access and process XML documents.

## Constants

The DBMS_XMLDOM system package defines a set of constants for use by DBMS_XMLDOM advanced package related functions and subroutines. The table below provides detailed information.

|Name   |Data Type  |Value|Description         |
| ----------- | ----------|-----------|-----------|
| ELEMENT_NODE                |INTEGER | 1   | An Element node       |
| ATTRIBUTE_NODE              |INTEGER | 2   | An Attribute node       |
| TEXT_NODE                   |INTEGER | 3   | A Text node       |
| CDATA_SECTION_NODE          |INTEGER | 4   | Reserved Fields       |
| ENTITY_REFERENCE_NODE       |INTEGER | 5   | Reserved Fields       |
| ENTITY_NODE                 |INTEGER | 6   | Reserved Fields       |
| PROCESSING_INSTRUCTION_NODE |INTEGER | 7   | Reserved Fields       |
| COMMENT_NODE                |INTEGER | 8   | A Comment node        |
| DOCUMENT_NODE               |INTEGER | 9   | A Document node       |
| DOCUMENT_TYPE_NODE          |INTEGER | 10  | A Document Type Definition node       |
| DOCUMENT_FRAGMENT_NODE      |INTEGER | 11  | Reserved Fields       |
| NOTATION_NODE               |INTEGER | 12  | A Notation node       |

## Data Structure

The DBMS_XMLDOM system package defines a set of data structures for use by DBMS_XMLDOM advanced package related functions and subroutines. The table below provides detailed information.

|Name   |Data Type  |Description         |
| ------------- | ------- | ----------------------- |
| DOMDocument   |RECORD  | Implements the DOM Document interface.         |
| DOMNode       |RECORD  | Implements the DOM Node interface.     |
| DOMNodeList   |RECORD  | Implements the DOM NodeList interface.          |
| DOMAttr       |RECORD  | Implements the DOM Attribute interface.       |
| DOMElement    |RECORD  | Implements the DOM Element interface.           |
| DOMText       |RECORD  | Implements the DOM Text interface.           |


## Subprograms Summary of DBMS_XMLDOM

### appendChild Function

This function is used to append a child node to a specified parent node, with the appended child node inserted at the end of the parent's child list.

```plsql
DBMS_XMLDOM.appendChild(n IN DOMNode, newChild IN DOMNode) RETURN DOMNode;
```

|Parameter |Purpose |
| --- | --- |
| n      | The parent node wanted to add a child node   |
| newChild  | The child node need to be inserted     |

### createElement Functions

These functions are used to create an element node to a XML document.

```plsql
DBMS_XMLDOM.createElement(doc IN DOMDocument, tagName IN VARCHAR2) RETURN DOMElement;
```

```plsql
DBMS_XMLDOM.createElement(doc IN DOMDocument, tagName IN VARCHAR2, ns IN VARCHAR2) RETURN DOMElement;
```

|Parameter |Purpose |
| --- | --- |
| doc    | The XML document where the new element node will be created. If the specified doc is NULL, returns NULL; if the specified doc has already been released, raises an error.   |
| tagName  | Config the name of the new element  |
| ns     | Specify the name space of the new element   |

**Example**

```plsql
DECLARE
  doc DBMS_XMLDOM.DOMDocument;
  root_node DBMS_XMLDOM.DOMElement;
BEGIN
  -- Create XML DOMDocument object
  doc := DBMS_XMLDOM.newDOMDocument;

  -- Create root node, operation successful when document is not released 
  root_node := DBMS_XMLDOM.createElement(doc, 'Root'); 

  -- Free the DOMDocument
  DBMS_XMLDOM.freeDocument(doc); 

  -- Attempting to create nodes again in a released document will trigger an error
  root_node := DBMS_XMLDOM.createElement(doc, 'NewRoot'); 
END;
/

YAS-07202 plugin execution error, invalid PL/SQL DOM handle
YAS-04015 at "SYS.DBMS_XMLDOM.CREATEELEMENT", line 78
YAS-04015 at line 19

```

### createTextNode Function

This function is used to create a text node to a XML document.

```plsql
DBMS_XMLDOM.createTextNode(doc IN DOMDocument, data IN VARCHAR2) RETURN DOMText;
```

|Parameter |Purpose |
| --- | --- |
| doc    | The XML document where the new text node will be created. If the specified doc is NULL, returns NULL; if the specified doc has already been released, raises an error.   |
| data  | Content needed to add to the XML DOMdocument as a DOMText node  |


### freeDocument Procedure

This procedure is used to free a XML document object.

```plsql
DBMS_XMLDOM.freeDocument(doc IN DOMDocument);
```

|Parameter |Purpose |
| --- | --- |
| doc    | The XML document where the new element will be created. If the specified doc is NULL, returns NULL.   |

### getAttribute Functions

This function is used to get an attribute value of a specified element node or NULL otherwise.

```plsql
DBMS_XMLDOM.getAttribute(elem IN DOMElement, name IN VARCHAR2) RETURN varchar2;
```

```plsql
DBMS_XMLDOM.getAttribute(elem IN DOMElement, name IN VARCHAR2, ns IN VARCHAR2) RETURN varchar2;
```

|Parameter |Purpose |
| --- | --- |
| elem    | The element   |
| Name  | The attribute  |
| ns     | The name space of the element   |

### getChildNodes Function

This function is used to get all the children node list of the specified node.

```plsql
DBMS_XMLDOM.getChildNodes(n IN DOMNode) RETURN DOMNodeList;
```

|Parameter |Purpose |
| --- | --- |
| n      | DOMNode name   |


### getChildrenByTagName Functions

These functions are used to get the children of the specified element.

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

|Parameter |Purpose |
| --- | --- |
| elem    | The element   |
| Name  | The attribute  |
| ns     | The name space of the element   |

### getDocumentElement Function

This function is used to get the root element of the XML document object.

```plsql
DBMS_XMLDOM.getDocumentElement(doc IN DOMDocument) RETURN DOMElement;
```

|Parameter |Purpose |
| --- | --- |
| doc    | The DOMDocument   |

### getElementsByTagName Functions

These functions are used to get the children node list of all the elements with a specified tagname. 

If the search object is empty (DOMElement or DOMDocument), or if no node name is specified, the function returns NULL.

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

|Parameter |Purpose |
| --- | --- |
| elem    | The DOMElement   |
| Name  | Specified the name of the Node  |
| ns     | Specified the name space of the element   |
| doc    | Specified the DOMDocument   |
| tagName  | Tag name; using a wildcard(*) would match any tag  |

### getFirstChild Function

This function is used to get the first children node of the specified node.

```plsql
DBMS_XMLDOM.getFirstChild(n IN DOMNode) RETURN DOMNode;
```

|Parameter |Purpose |
| --- | --- |
| n      | DOMNode name   |

### getLength Function

This function is used to get the number of nodes from a node list, and requires other functions to first obtain the node list.

```plsql
DBMS_XMLDOM.getLength(nl IN DOMNodeList) RETURN INTEGER;
```

|Parameter |Purpose |
| --- | --- |
| nl      | DOMNodeList    |

### getNodeName Function

This function is used to get the name of the specified node.

```plsql
DBMS_XMLDOM.getNodeName(n IN DOMNode) RETURN VARCHAR2;
```

|Parameter |Purpose |
| --- | --- |
| n      | DOMNode name   |

### getNodeValue Function

This function is used to get the value of the specified node.

```plsql
DBMS_XMLDOM.getNodeValue(n IN DOMNode) RETURN VARCHAR2;
```

|Parameter |Purpose |
| --- | --- |
| n      | DOMNode name   |

### getParentNode Function

This function is used to get the parent node of the specified node.

```plsql
DBMS_XMLDOM.getParentNode(n IN DOMNode) RETURN DOMNode;
```

|Parameter |Purpose |
| --- | --- |
| n      | DOMNode name   |

### getValue Function

This function is used to get the value of the specified attribute.

```plsql
DBMS_XMLDOM.getValue (a IN DOMAttr) RETURN VARCHAR2;
```

|Parameter |Purpose |
| --- | --- |
| a      | Attribute name   |

### isNull Function

This function is used to check the XML document is NULL; returns TRUE if it is NULL, FALSE otherwise.

```plsql
DBMS_XMLDOM.isNull(doc IN DOMDocument) RETURN BOOLEAN;
```

|Parameter |Purpose |
| --- | --- |
| doc    | Specified the DOMDocument   |

### item Function

This is used to get the item in the collection which corresponds to the INDEX parameter. 

```plsql
DBMS_XMLDOM.item(nl IN DOMNodeList, idx IN INTEGER) RETURN DOMNode;
```

|Parameter |Purpose |
| --- | --- |
| nl      | The DOMNodeList. If the input DOMNodeList is NULL, the function returns NULL .   |
| idx     | The index of the Node. <br>- When the input index is NULL, it returns the first node.<br>- If index is greater than or equal to the number of nodes in the list, this function returns NULL.   |

### makeElement Function

This is used to cast the specified node to an element node.

```plsql
DBMS_XMLDOM.makeElement(n IN DOMNode) RETURN DOMElement;
```

|Parameter |Purpose |
| --- | --- |
| n      | DOMNode name   |

### makeNode Functions

These functions are used to cast the DOMDocument, text, attribute, or element to a DOMNODE.

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

|Parameter |Purpose |
| --- | --- |
| doc    | Specified the DOMDocument   |
| t      | Specified the DOMText   |
| a      | Specified the DOMAttr   |
| elem    | Specified the DOMElement   |

**Example**

```plsql
DECLARE
  doc DBMS_XMLDOM.DOMDocument;
  book_element DBMS_XMLDOM.DOMElement;
  new_node DBMS_XMLDOM.DOMNode;
BEGIN
  doc := DBMS_XMLDOM.newDOMDocument();

  book_element := DBMS_XMLDOM.createElement(doc, 'book');
  
  -- Append an element node to root node
  new_node := DBMS_XMLDOM.appendChild(DBMS_XMLDOM.makeNode(doc), DBMS_XMLDOM.makeNode(book_element));
END;
/

PL/SQL Succeed.
```

### newDomDocument Function

These functions are used to creat a new XML document object.

Create an empty DOMDocument.

```plsql
DBMS_XMLDOM.newDomDocument
  RETURN DOMDOCUMENT;
```

Create a DOMDocument based on the input xmltype file.

```plsql
DBMS_XMLDOM.newDomDocument
 (
    xmldoc IN sys.xmltype)
  RETURN DOMDOCUMENT;
```

Create a DOMDocument based on the input clob data.

```plsql
DBMS_XMLDOM.newDomDocument
 (
    c IN clob)
  RETURN DOMDOCUMENT;
```

|Parameter |Purpose |
| --- | --- |
| xmldoc | Document source in XMLType for the DOMDOCUMENT |
| c      | Data source in CLOB data for the DOMDOCUMENT   |


### setAttribute Procedures

These procedures are used to add a sttribute value for the specified element.

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

|Parameter |Purpose |
| --- | --- |
| elem    | Specified the DOMElement   |
| Name    | Specified the Attribute name   |
| newvalue    | Config the Attribute value   |
| ns     | Specified the name space of the element   |

### setNodeValue Procedure

Theis procedure is used to set a value to the specified node.

```plsql
DBMS_XMLDOM.setNodeValue(n IN DOMNode, nodeValue IN VARCHAR2);
```

|Parameter |Purpose |
| --- | --- |
| n    | Specified the DOMNode   |
| nodeValue    | The value to which node is set   |

### writeToBuffer Procedures

These procedures are used to write the XML document object or XML DOMNode to a specified buffer.

```plsql
DBMS_XMLDOM.writeToBuffer(n IN DOMNode, buffer IN OUT VARCHAR2);
```

```plsql
DBMS_XMLDOM.writeToBuffer(doc IN DOMDocument, buffer IN OUT VARCHAR2);
```

|Parameter |Purpose |
| --- | --- |
| n    | Specified the DOMNode   |
| doc    | Specify the DOMDOCUMENT   |
| buffer | Buffer to which to write  |


**Example**

```plsql
DECLARE
  doc DBMS_XMLDOM.DOMDocument;
  buffer CLOB;
  new_node DBMS_XMLDOM.DOMNode;
BEGIN
  -- Create a new XML DOMDocument
  doc := DBMS_XMLDOM.newDOMDocument;
  
  -- Append the root element to DOMDocument
  new_node := 
  DBMS_XMLDOM.appendChild(
    DBMS_XMLDOM.makeNode(doc), 
    DBMS_XMLDOM.makeNode(DBMS_XMLDOM.createElement(doc, 'Root'))
  );
  
  -- Write the XML DOMDocument to buffer
  DBMS_XMLDOM.writeToBuffer(doc, buffer);
  
  -- Get the length of the DOMDocument in the buffer
  DBMS_OUTPUT.PUT_LINE('Buffer Size: ' || DBMS_LOB.getLength(buffer) || ' bytes');
  
  DBMS_XMLDOM.freeDocument(doc);
END;
/

--result

Buffer Size: 38 bytes

PL/SQL Succeed.
```

## Example 

Create an empty document, add multiple element nodes under the element node with the same key value.

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
