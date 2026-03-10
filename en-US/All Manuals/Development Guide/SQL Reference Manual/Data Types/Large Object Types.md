The LOB (Large Object) type is used to store large variable-length binary or text data in the database.

YashanDB's LOB types include BLOB, CLOB, and NCLOB.

## Storage Attributes

|Type |Byte Length |
| -------- | ---------------------------------------------- |
| BLOB     | 1~4G*DB_BLOCK_SIZE  |
| CLOB     | 1~4G*DB_BLOCK_SIZE  |
| NCLOB    | Row storage:<br/>1~4G*DB_BLOCK_SIZE<br/>Column storage:<br/>No such type   |

Definition format:

|Type |Format |Rules |
| -------- | ----- | ------------------------------------- |
| BLOB     | BLOB  | Variable-length binary string, no size needed   |
| CLOB     | CLOB  | Variable-length string, no size needed             |
| NCLOB    | NCLOB | Variable-length string supporting UNICODE, no size needed |

YashanDB provides two methods for storing large object types: inline storage and out-of-line storage:

- When the LOB column data of a row is less than a certain byte limit, the LOB data is stored inline. For HEAP tables, this limit is 4000 bytes; for TAC/LSC tables, this limit is 32000 bytes.
- When exceeding the above byte limit, the LOB data is stored in a separate LOB space (a tablespace can be specified for it), while inline storage contains a pointer to the LOB data.

> **Note**: 
>
> When data is stored, some internal metadata is generated. The byte limit for inline storage in HEAP tables includes the space occupied by this metadata, whereas the byte limit for LSC tables (TAC and LSC tables) does not include the space occupied by metadata.
> 
> Taking DB_BLOCK_SIZE = 8K as an example, the maximum byte length of LOB type data in heap tables is `4G*8K=32T`.

## Usage Rules

<span id="lob_use_restrictions" name="lob_use_restrictions" class="yaslink"></span>

### Usage Restrictions

YashanDB has the following restrictions on the use of LOB types:

- Cannot be used as index columns
- Cannot be used as sort columns
- Cannot modify the data type of LOB columns
- Cannot be used as partition keys
- Cannot be involved in arithmetic operations or modulus operations with other data types
- Cannot be used as comparison conditions
- Cannot use DISTINCT for deduplication
- Cannot be used for GROUP BY queries

### BLOB

BLOB represents a binary large object, such as images, videos, audio files, etc. For storing such files, one can either store only the file address in the database and retrieve the files via links or directly store the binary data as a BLOB field in the database table.

Since BLOB content is binary data, except for the [implicit data conversion with RAW type](./RAW), this data type does not participate in any other type conversions or operations in SQL. However, comparisons with BLOB/RAW typed data can be achieved using [DBMS_LOB](../../PL参考手册/内置高级包/DBMS_LOB).COMPARE. This advanced package also provides some operational methods for the BLOB type.

In LSC tables, BLOBs perform UTF-8 character set validation when they need to be converted to strings in functions.

***Example***

```sql
-- 1. Create a customer_img table with a BLOB field under the sales user
CREATE TABLE customer_img (id INT, logo BLOB);
```

This example uses the YashanDB JDBC driver client program to insert a record containing an image into customer_img. The example refers to the getConnection method in the [YashanDB JDBC driver usage examples](../../JDBC驱动/JDBC驱动使用介绍).

```java
// 2. Create a Java file
package jdbc0;
import java.io.FileInputStream;
import java.sql.Connection;
import java.sql.SQLException;
import java.sql.PreparedStatement;

public class Blobexample {
    public static void main(String[] args) throws Exception {
         // Create a database connection.
        Connection conn = Jdbcexample.getConnection("sales", "sales");
		
		PreparedStatement pst = null;
        try {
            // Generate the prepared statement.
            pst = conn.prepareStatement("INSERT INTO customer_img VALUES (?,?)");
            // Add parameters.
            pst.setInt(1, 1);
            // Read the image file and convert it to binary
            FileInputStream in = new FileInputStream("./logo.png");
									
            pst.setBytes(2, trans2Byte(in));
            pst.executeUpdate();
            System.out.println("insert table customer_img succeed!");
            pst.close();
        } catch (SQLException e) {
            if (pst != null) {
                pst.close();
            }
			e.printStackTrace();
        }
    }
	
	public static byte[] trans2Byte(FileInputStream in) {
		int count = 0;
		try {
			while (count == 0) {
				count = in.available();
			}
			
			byte[] blob = new byte[count];
			in.read(blob);
			return blob;
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}
}  
```

```shell
# 3. Compile and run in Linux
$ javac -d . Blobexample.java
$ java jdbc0.Blobexample
Connection succeed!
insert table customer_img succeed!
```

```sql
-- 4. Query the inserted data (partial display)
SELECT id,logo FROM customer_img;
          ID LOGO                                                            
------------ ---------------------------------------------------------------- 
           1 89504E470D0A1A0A0000000D49484452000000FA000000440806000000A82C28380000000467414D410000B18F0BFC610500000038655849664D4D002A00000008000187690004000000010000001A000000000002A0020004
```

### CLOB

CLOB represents variable-length text, similar to the VARCHAR type. The maximum storage specification of VARCHAR type is 65534 bytes. For fields that might store data exceeding this specification, they can be set as CLOB type.

- All CLOB type data will undergo character set validation when sent to the client. Verification failures will return an error.

- CLOB type supports most character functions and can participate in calculations after executing data conversion. Specific details can be found in the description of each function.

Comparisons between CLOB and CLOB/CHAR/VARCHAR type data can be achieved using [DBMS_LOB](../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_LOB).COMPARE. This advanced package also offers some operational methods for the CLOB type.

***Example***

```sql
-- 1. Create a customer_intro_clob table with a CLOB field under the sales user
CREATE TABLE customer_intro_clob (id INT, intro CLOB);

-- 2. Insert CLOB data
INSERT INTO customer_intro_clob 
VALUES (1, 'It gives me great pleasure to introduce our company.');
COMMIT;

-- 3. CLOB data participates in character arithmetic
SELECT SUBSTRING(intro, 3,5) FROM customer_intro_clob WHERE id = 1;
SUBSTRING(INTRO,3,5)                                             
---------------------------------------------------------------- 
 give       
```

### NCLOB

NCLOB stores UNICODE variable-length data, functioning similarly to CLOB type, and can store a maximum of 1~4G*DB_BLOCK_SIZE data.

The NCLOB type is only applicable to HEAP tables.

***Example*** for Heap tables

```sql
-- 1. Create the NCLOB_TABLE
CREATE TABLE nclob_table (c1 NCLOB);

-- 2. Insert NCLOB data
INSERT INTO nclob_table 
VALUES ('It gives me great pleasure to introduce our company.');
COMMIT;

-- 3. NCLOB data conversion
SELECT CAST(c1 AS CHAR(90)) FROM nclob_table;
CAST(C1ASCHAR(90))                                               
---------------------------------------------------------------- 
It gives me great pleasure to introduce our company.                                   
```
