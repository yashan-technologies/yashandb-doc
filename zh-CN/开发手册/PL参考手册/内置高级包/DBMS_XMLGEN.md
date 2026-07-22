DBMS_XMLGEN高级包提供了一组内置的存储过程、常量、数据结构，用于将查询的结果数据转换为标准的XML格式文档。

当前DBMS_XMLGEN高级包应用需遵循如下用法：

- XMLGEN一次性转换XML格式的查询结果集大小不能超过64MB，如果查询结果集超过限定值请分批转换。
- 支持转换成XML节点取值的查询结果数据最大长度不超过64KB。
- 生成XML格式文档时，支持对文本中的特殊字符定义编码规则。

## 数据结构

DBMS_XMLGEN包中使用自定义数据类型作为函数参数或返回值。

```plsql
SUBTYPE CTXHANDLE IS NUMBER;
SUBTYPE CTXTYPE IS NUMBER;
SUBTYPE CONVERSIONTYPE IS NUMBER;
```

## 预定义常量

DBMS_XMLGEN系统包定义了一组常量，供DBMS_XMLGEN高级包相关函数及子程序调用。下表为详细信息。

| 名称| 数据类型| 值| 描述|
| ----------- | ----------|-----------|-----------|
| NONE                | NUMBER | 0   | 用于控制生成XML格式文档时是否生成元数据信息。       |
| DTD              | NUMBER | 1   | 生成XML格式文档时嵌入DTD `<!DOCTYPE ...>`，用于定义元素和属性的合法结构。       |
| SCHEMA                   | NUMBER | 2   | 生成XML格式文档时嵌入XML Schema（XSD），用于定义XML文档的命名空间和SCHEMA。       |
| ENTITY_ENCODE          | NUMBER | 0   | 将文本中的特殊字符转换为对应的XML实体引用（entity references），以确保这些字符在XML中被安全解析，而不会破坏文档结构。       |
| ENTITY_DECODE       | NUMBER | 1   | 将XML文档中的实体引用还原为原始的特殊字符。       |
| DROP_NULLS      | NUMBER | 0   | 表示数据中的NULL值不会生成空的元素节点。       |
| NULL_ATTR                | NUMBER | 1   | 表示数据中的NULL值会生成完整的空的元素节点，并添加属性`xsi:nil="true"`，其中xsi为XML文档的命名空间。<br> 设置后，命名空间会变为：xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"。|
| EMPTY_TAG               | NUMBER | 2   | 表示数据中的NULL值以自闭合标签`<TAG/>`生成空值元素节点。      |

## 子程序说明

###  CLOSECONTEXT Procedure

```plsql
DBMS_XMLGEN.CLOSECONTEXT(ctx IN CTXHANDLE);
```

该存储过程用于结束XML格式文档生成操作，释放系统资源。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |

### CONVERT Function

```plsql
DBMS_XMLGEN.CONVERT(xmlData IN VARCHAR2, flag IN BIGINT DEFAULT ENTITY_ENCODE) RETURN VARCHAR2;

DBMS_XMLGEN.CONVERT(xmlData IN CLOB, flag IN BIGINT DEFAULT ENTITY_ENCODE) RETURN CLOB;
```

该函数用于将XML数据中的特殊符号转换成符合XML规范的实体。

该函数暂不支持入参为绑定参数。

|  参数| 用途|
| --- | --- |
| xmlData    | 可以直接传入字符串类型的XML格式文档，也可以传入GETXML函数或子程序返回的CLOB类型的XML格式文档。   |
| flag  | 用于配置对XML数据中特殊符号的转换方式。默认为ENTITY_ENCODE，也可以传入0，表示需要对XML数据中的特殊符号进行编码；传入其他非0数值或ENTITY_DECODE时，表示不对XML数据中的特殊符号进行编码。   |

此函数影响的特殊符号如下表所示。

|  符号| 编码|
| --- | --- |
| & |   &amp; | 
| < |    &lt; |
| > | &gt; |
| " |    &quot; |
| ' |    &apos; | 

### GETNUMROWSPROCESSED Function

```plsql
DBMS_XMLGEN.GETNUMROWSPROCESSED(ctx IN CTXHANDLE) RETURN NUMBER;
```

该函数用于获取本次生成XML格式文档需要处理的SQL查询结果数据总行数。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |

### GETXML Function and Procedure

```plsql
-- 存储过程
DBMS_XMLGEN.GETXML(ctx IN CTXHANDLE, tmpclob IN OUT NOCOPY clob, dtdOrSchema IN NUMBER DEFAULT NONE);

-- 函数
DBMS_XMLGEN.GETXML(ctx IN CTXHANDLE, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN CLOB;
DBMS_XMLGEN.GETXML(sqlQuery IN VARCHAR2, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN CLOB;
```

该函数将查询结果转换为XML文档格式，并以CLOB格式返回。

该函数暂不支持入参为绑定参数。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |
| sqlQuery  | 输入查询SQL语句。   |
| tmpclob   | 将查询结果转成XML格式并以CLOB类型返回。<br> 使用GEMXML子存储过程时，会将原始数据和转换后的数据拼接后返回。   |
| dtdOrSchema     | 配置生成的XML格式文档是否包含DTD或Schema信息。<br>- 默认传入None，也可以传入0，表示不包含DTD和Schema信息；<br>- 入参传入1或DTD时，表示添加DTD信息。当前仅完成兼容性，无具体含义。<br>- 入参传入2或SCHEMA时，表示添加SCHEMA信息。当前仅完成兼容性，无具体含义。   |

### GETXMLTYPE Function and Procedure

```plsql
-- 存储过程
DBMS_XMLGEN.GETXMLTYPE(ctx IN CTXHANDLE, tmpxmltype IN OUT NOCOPY XMLTYPE, dtdOrSchema IN NUMBER DEFAULT NONE);

-- 函数
DBMS_XMLGEN.GETXMLTYPE(ctx IN CTXHANDLE, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN sys.XMLTYPE;
DBMS_XMLGEN.GETXMLTYPE(sqlQuery IN VARCHAR2, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN sys.XMLTYPE;
```

该函数将查询结果转换为XML文档格式，并以XMLTYPE格式返回。

该函数暂不支持入参为绑定参数。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |
| sqlQuery  | 输入查询SQL语句。   |
| tmpxmltype   | 将查询结果转成XML格式并以XMLTYPE类型返回。<br> 使用GEMXMLTYPE子存储过程时，会将原始数据和转换后的数据拼接后返回。   |
| dtdOrSchema     | 配置生成的XML格式文档是否包含DTD或Schema信息。<br>- 默认传入None，也可以传入0，表示不包含DTD和Schema信息；<br>- 入参传入1或DTD时，表示添加DTD信息。当前仅完成兼容性，无具体含义。<br>- 入参传入2或SCHEMA时，表示添加SCHEMA信息。当前仅完成兼容性，无具体含义。   |

### NEWCONTEXT Function

```plsql
DBMS_XMLGEN.NEWCONTEXT(queryString IN VARCHAR2) RETURN CTXHANDLE;
DBMS_XMLGEN.NEWCONTEXT(queryString IN SYS_REFCURSOR) RETURN CTXHANDLE;
```

该函数用于传入SQL查询语句。

SQL查询语句可以通过入参直接传入字符串，也可以通过系统动态游标SYS_REFCURSOR传入，并返回CTXHANDLE。

###  SETMAXROWS Procedure

```plsql
DBMS_XMLGEN.SETMAXROWS(ctx IN CTXHANDLE, maxRows IN NUMBER);
```

该存储过程用于配置本次生成XML格式文档要处理的SQL查询数据的最大行数，如果SQL查询数据总行数超过配置值，达到配置值后结束XML格式转换。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |
| maxRows  | 配置最大转换行数。    |

###  SETNULLHANDLING Procedure

```plsql
DBMS_XMLGEN.SETNULLHANDLING(ctx IN CTXHANDLE, flag IN NUMBER DEFAULT DROP_NULLS);
```

该存储过程用于配置生成XML格式文档时对查询数据中列值为NULL的处理方式。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |
| flag     | 配置生成XML格式文档时对列值为NULL的处理方式。<br>- 默认传入DROP_NULLS，也可以传入0，表示不会生成空的元素节点；<br>- 入参传入1或NULL_ATTR时，表示生成完整的空的元素节点，并添加属性`xsi:nil="true"`，其中xsi为XML文档的命名空间。设置后，命名空间会变为：xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"。<br>- 入参传入2或EMPTY_TAG时，表示以自闭合标签`<TAG/>`方式生成空的元素节点。   |

###  SETSKIPROWS Procedure

```plsql
DBMS_XMLGEN.SETSKIPROWS(ctx IN CTXHANDLE, skipRows IN NUMBER);
```

该存储过程配置本次生成XML格式文档要跳过的SQL查询数据的最大行数，从配置值的下一行开始XML格式转换。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |
| skipRows  | 配置跳过行数。    |

###  SETROWTAG Procedure

```plsql
DBMS_XMLGEN.SETROWTAG(ctx IN CTXHANDLE, rowTagName IN VARCHAR2);
```

该存储过程用于在生成XML格式文档时自定义指定每行数据对应节点的标签名称。默认情况下，每行数据对应的节点标签为`ROW`。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |
| rowTagName  | 自定义每行数据节点的标签名称。   |

###  SETROWSETTAG Procedure

```plsql
DBMS_XMLGEN.SETROWSETTAG(ctx IN CTXHANDLE, rowsTagName IN VARCHAR2);
```

该存储过程用于在生成XML格式文档时自定义指定结果集外层节点的标签名称。默认情况下，结果集外层节点标签为`ROWSET`。

|  参数| 用途|
| --- | --- |
| ctx    | 将NEWCONTEXT函数返回值作为入参。   |
| rowsTagName  | 自定义结果集外层节点的标签名称。   |

### 示例

```sql
-- 创建表并插入数据
CREATE TABLE xmlgen_basic_employees (
                           employee_id NUMBER PRIMARY KEY,
                           first_name VARCHAR2(50),
                           last_name VARCHAR2(50),
                           email VARCHAR2(100),
                           department VARCHAR2(50),
                           salary NUMBER(10,2)
);

INSERT INTO xmlgen_basic_employees VALUES (1, 'John', 'Smith', 'john.smith@company.com', 'IT', 75000.00);
INSERT INTO xmlgen_basic_employees VALUES (2, 'Jane', 'Doe', 'jane.doe@company.com', 'HR', 65000.00);
INSERT INTO xmlgen_basic_employees VALUES (3, 'Michael', 'Johnson', 'michael.johnson@company.com', 'Finance', 80000.00);
INSERT INTO xmlgen_basic_employees VALUES (4, 'Sarah', 'Williams', 'sarah.williams@company.com', 'IT', 70000.00);
INSERT INTO xmlgen_basic_employees VALUES (5, 'David', 'Brown', 'david.brown@company.com', 'Marketing', 60000.00);
INSERT INTO xmlgen_basic_employees VALUES (6, 'Lisa', 'Davis', 'lisa.davis@company.com', 'HR', 68000.00);
INSERT INTO xmlgen_basic_employees VALUES (7, 'Robert', 'Miller', 'robert.miller@company.com', 'Finance', 85000.00);
INSERT INTO xmlgen_basic_employees VALUES (8, 'Jennifer', 'Wilson', 'jennifer.wilson@company.com', 'IT', 72000.00);
INSERT INTO xmlgen_basic_employees VALUES (9, NULL, 'Taylor', 'taylor@company.com', 'Marketing', 58000.00);
INSERT INTO xmlgen_basic_employees VALUES (10, 'Kevin', 'Anderson', NULL, 'Operations', 62000.00);

COMMIT;

-- 验证基础的XML格式文档生成能力
select dbms_xmlgen.getxml('select 1 as "-A" from dual') from dual;

DBMS_XMLGEN.GETXML('SELECT1AS"-A"FROMDUAL')                      
---------------------------------------------------------------- 
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET>
  <ROW>
    <_x002D_A>1</_x002D_A>
  </ROW>
</ROWSET>

-- 验证对特殊字符的编码能力，flag字段入参为空表示编码，flag入参为1表示不编码。
SELECT DBMS_XMLGEN.CONVERT('<>&"''') FROM dual;

DBMS_XMLGEN.CONVERT('<>&"''')                                  
---------------------------------------------------------------- 
&lt;&gt;&amp;&quot;&apos;                                       


SELECT DBMS_XMLGEN.CONVERT('<>&"''',1) FROM dual;

DBMS_XMLGEN.CONVERT('<>&"''',1)                                  
---------------------------------------------------------------- 
<>&"'   

```

以下示例为按CLOB类型生成XML格式文档，并控制空值为NULL_ATTR和DROP_NULLS处理方式。

```plsql
DECLARE
    ctx_handle NUMBER;
    xml_result CLOB;
BEGIN
    DBMS_LOB.CREATETEMPORARY(xml_result);
    -- Create context for XML generation
    ctx_handle := DBMS_XMLGEN.newContext('SELECT employee_id, first_name, last_name, email FROM xmlgen_basic_employees WHERE employee_id IN (9, 10) ORDER BY employee_id');

    -- Set null handling to show NULL values as empty tags (default behavior)
    DBMS_XMLGEN.setNullHandling(ctx_handle, DBMS_XMLGEN.NULL_ATTR);

    -- Generate XML output with NULL attributes
    DBMS_OUTPUT.PUT_LINE('XML with NULL handling using NULL_ATTR:');
    xml_result := DBMS_XMLGEN.getXML(ctx_handle);
    DBMS_OUTPUT.PUT_LINE(xml_result);

    -- Reset context and change null handling
    DBMS_XMLGEN.closeContext(ctx_handle);
    ctx_handle := DBMS_XMLGEN.newContext('SELECT employee_id, first_name, last_name, email FROM xmlgen_basic_employees WHERE employee_id IN (9, 10) ORDER BY employee_id');

    -- Set null handling to omit NULL elements entirely
    DBMS_XMLGEN.setNullHandling(ctx_handle, DBMS_XMLGEN.DROP_NULLS);

    -- Generate XML output with omitted NULL values
    DBMS_OUTPUT.PUT_LINE('XML with NULL handling using DROP_NULLS:');
    xml_result := DBMS_XMLGEN.getXML(ctx_handle);
    DBMS_OUTPUT.PUT_LINE(xml_result);

    -- Close the context
    DBMS_XMLGEN.closeContext(ctx_handle);
    DBMS_LOB.FREETEMPORARY(xml_result);
END;
/

XML with NULL handling using NULL_ATTR:
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ROW>
    <EMPLOYEE_ID>9</EMPLOYEE_ID>
    <FIRST_NAME xsi:nil="true"/>
    <LAST_NAME>Taylor</LAST_NAME>
    <EMAIL>taylor@company.com</EMAIL>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>10</EMPLOYEE_ID>
    <FIRST_NAME>Kevin</FIRST_NAME>
    <LAST_NAME>Anderson</LAST_NAME>
    <EMAIL xsi:nil="true"/>
  </ROW>
</ROWSET>

XML with NULL handling using DROP_NULLS:
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET>
  <ROW>
    <EMPLOYEE_ID>9</EMPLOYEE_ID>
    <LAST_NAME>Taylor</LAST_NAME>
    <EMAIL>taylor@company.com</EMAIL>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>10</EMPLOYEE_ID>
    <FIRST_NAME>Kevin</FIRST_NAME>
    <LAST_NAME>Anderson</LAST_NAME>
  </ROW>
</ROWSET>

PL/SQL Succeed.
```

以下示例为按XMLTYPE类型生成XML格式文档，并控制本次最多完成5行数据转换。

```plsql
DECLARE
    ctx_handle NUMBER;
    xml_result xmltype;
    rows_processed NUMBER;
BEGIN
    -- Create context for XML generation
    ctx_handle := DBMS_XMLGEN.newContext('SELECT employee_id, first_name, last_name, department, salary FROM xmlgen_basic_employees ORDER BY salary DESC');

    -- Limit to only 5 rows maximum
    DBMS_XMLGEN.setMaxRows(ctx_handle, 5);

    -- Generate XML output
    xml_result := DBMS_XMLGEN.getXmltype(ctx_handle);

    -- Get number of rows processed
    rows_processed := DBMS_XMLGEN.getNumRowsProcessed(ctx_handle);

    -- Display results
    DBMS_OUTPUT.PUT_LINE('Top 5 highest paid employees in XML format:');
    DBMS_OUTPUT.PUT_LINE(xml_result.getClobval());
    DBMS_OUTPUT.PUT_LINE('Number of rows processed: ' || rows_processed);

    -- Close the context
    DBMS_XMLGEN.closeContext(ctx_handle);
END;
/

Top 5 highest paid employees in XML format:
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET>
  <ROW>
    <EMPLOYEE_ID>7</EMPLOYEE_ID>
    <FIRST_NAME>Robert</FIRST_NAME>
    <LAST_NAME>Miller</LAST_NAME>
    <DEPARTMENT>Finance</DEPARTMENT>
    <SALARY>85000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>3</EMPLOYEE_ID>
    <FIRST_NAME>Michael</FIRST_NAME>
    <LAST_NAME>Johnson</LAST_NAME>
    <DEPARTMENT>Finance</DEPARTMENT>
    <SALARY>80000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>1</EMPLOYEE_ID>
    <FIRST_NAME>John</FIRST_NAME>
    <LAST_NAME>Smith</LAST_NAME>
    <DEPARTMENT>IT</DEPARTMENT>
    <SALARY>75000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>8</EMPLOYEE_ID>
    <FIRST_NAME>Jennifer</FIRST_NAME>
    <LAST_NAME>Wilson</LAST_NAME>
    <DEPARTMENT>IT</DEPARTMENT>
    <SALARY>72000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>4</EMPLOYEE_ID>
    <FIRST_NAME>Sarah</FIRST_NAME>
    <LAST_NAME>Williams</LAST_NAME>
    <DEPARTMENT>IT</DEPARTMENT>
    <SALARY>70000</SALARY>
  </ROW>
</ROWSET>

Number of rows processed: 5

PL/SQL Succeed.
```

以下示例为自定义XML标签名称，包括行节点标签和结果集外层节点标签。

```plsql
DECLARE
    ctx_handle NUMBER;
    xml_result CLOB;
BEGIN
    DBMS_LOB.CREATETEMPORARY(xml_result);
    -- Create context for XML generation
    ctx_handle := DBMS_XMLGEN.newContext('SELECT employee_id, first_name, last_name FROM xmlgen_basic_employees WHERE employee_id <= 3 ORDER BY employee_id');

    -- Customize the row tag name to "EMPLOYEE"
    DBMS_XMLGEN.setRowTag(ctx_handle, 'EMPLOYEE');

    -- Customize the rowset tag name to "STAFF"
    DBMS_XMLGEN.setRowSetTag(ctx_handle, 'STAFF');

    -- Generate XML output
    xml_result := DBMS_XMLGEN.getXML(ctx_handle);

    DBMS_OUTPUT.PUT_LINE('XML with custom tags:');
    DBMS_OUTPUT.PUT_LINE(xml_result);

    -- Close the context
    DBMS_XMLGEN.closeContext(ctx_handle);
    DBMS_LOB.FREETEMPORARY(xml_result);
END;
/

XML with custom tags:
<?xml version="1.0" encoding="UTF-8"?>
<STAFF>
  <EMPLOYEE>
    <EMPLOYEE_ID>1</EMPLOYEE_ID>
    <FIRST_NAME>John</FIRST_NAME>
    <LAST_NAME>Smith</LAST_NAME>
  </EMPLOYEE>
  <EMPLOYEE>
    <EMPLOYEE_ID>2</EMPLOYEE_ID>
    <FIRST_NAME>Jane</FIRST_NAME>
    <LAST_NAME>Doe</LAST_NAME>
  </EMPLOYEE>
  <EMPLOYEE>
    <EMPLOYEE_ID>3</EMPLOYEE_ID>
    <FIRST_NAME>Michael</FIRST_NAME>
    <LAST_NAME>Johnson</LAST_NAME>
  </EMPLOYEE>
</STAFF>

PL/SQL Succeed.
```
