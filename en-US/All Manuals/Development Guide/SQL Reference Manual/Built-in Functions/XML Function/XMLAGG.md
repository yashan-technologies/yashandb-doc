```ebnf+diagram
xmlagg::= XMLAGG "(" xml_data [order_by_clause]")"
```

XMLAGG is an aggregate function used to merge multiple rows of XML data into a single value of type XMLTYPE. The return type is XMLTYPE.

**xml_data**

[General Expression](../../General SQL Syntax/expr) for the XML data to be aggregated, which follows these rules:

* It must be of type XMLTYPE.
* The data length must not exceed 64MB.
* If the input is NULL, the return value will also be NULL.

**order_by_clause**

Specifies the sort order of the XML to be aggregated within the group, with syntax consistent with that described in the SELECT statement.

When a constant number is specified after ORDER BY, it denotes the order value of the XML.

***Example*** for Heap tables

```sql
-- Aggregate the XML data from employee_data
SELECT XMLAGG(xml_data ORDER BY id).GetClobVal() res FROM employee_data;

RES
----------------------------------------------------------------
<employee id="101">
                <name>John Smith</name>
                <salary>8500</salary>
              </employee><employee id="102">
                <name>Jane Doe</name>
                <salary>9200</salary>
              </employee><employee id="103">
                <name>Robert Johnson</name>
                <salary>7800</salary>
              </employee><employee id="104">
                <name>Emily Brown</name>
                <salary>9500</salary>
              </employee><employee id="105">
                <name>Michael Davis</name>
                <salary>8200</salary>
              </employee>


-- Input is NULL, returns empty
SELECT XMLAgg(NULL).GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
```
