The YashanDB [C Driver](../Development Guide/C Language Family Drivers/C Driver/YashanDB C Driver Interfaces/Vector Functions/00Vector Functions) and [Python yaspy Driver](../Development Guide/Python Driver/00Python Driver) support vector data processing interfaces. This section only provides examples of vector data operations. For detailed interface information, please refer to the corresponding driver documentation.

## Example with C Driver

```c
#include <string.h>
#include "yacli.h"

#define YYAC_CALL(proc)                           \
    do {                                         \
        if ((YacResult)(proc) != YAC_SUCCESS) {  \
            return YAC_ERROR;                    \
        }                                        \
    } while (0)

#define MB(x) (YacUint64)((YacUint64)(x) << 20U)

void printError()
{
    YacInt32   code;
    YacChar    msg[1000];
    YacTextPos pos;

    yacGetDiagRec(&code, msg, 1000, NULL, NULL, 0, &pos);
    if (pos.line != 0) {
        printf("[%d:%d]", pos.line, pos.column);
    }
    printf("YAC-%05d %s\n", code, msg);
}

typedef struct {
    YacHandle env;
    YacHandle conn;
    YacHandle stmt;
} YacTestEnv;

YacTestEnv gTestEnv = { 0 };

// Connect to the database
YacResult testConnect()
{
    // Change to the actual database server IP and port
    const YacChar* gSrvStr = "127.0.0.1:1688";
    const YacChar* user = "regress";
    const YacChar* pwd = "regress";

    YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &gTestEnv.env));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, gTestEnv.env, &gTestEnv.conn));
    YAC_CALL(yacConnect(gTestEnv.conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_STMT, gTestEnv.conn, &gTestEnv.stmt));

    return YAC_SUCCESS;
}
// Disconnect from the database
YacResult testDisConnect()
{
    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, gTestEnv.stmt));
    yacDisconnect(gTestEnv.conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, gTestEnv.conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, gTestEnv.env));

    return YAC_SUCCESS;
}

YacResult testVector()
{
    YacVector* dim2Vector = NULL;
    YacVector* dimMaxVector = NULL;
    YAC_CALL(yacDescAlloc2(gTestEnv.env, (YacVoid**)&dim2Vector, YAC_DESC_VECTOR));
    YAC_CALL(yacDescAlloc2(gTestEnv.env, (YacVoid**)&dimMaxVector, YAC_DESC_VECTOR));

    YacChar expectedDim2VecStr[100] = "[1.23456705E+000,2.12345672E+000]";
    YacUint32 len = strlen(expectedDim2VecStr);
    YAC_CALL(yacVectorFromText(dim2Vector, YAC_VECTOR_FORMAT_FLOAT32, 2, expectedDim2VecStr, len, 0));

    YacDouble curValue = 1.123456789012345;
    YacDouble vecArray[65535];
    for (YacUint32 idx = 0; idx < 65535; idx++) {
        vecArray[idx] = curValue;
        curValue += 1.0;
    }
    
    YAC_CALL(yacVectorFromArray(dimMaxVector, YAC_VECTOR_FORMAT_FLOAT64, 65535, (YacUint8*)vecArray, sizeof(YacDouble) * 65535, 0));
    YacChar* expectedMaxVecText = (YacChar*)malloc(MB(32));
    YacUint32 textLen = MB(32);
    YAC_CALL(yacVectorToText(dimMaxVector, expectedMaxVecText, &textLen, 0));

    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "drop table if exists yac_test_vector_tab", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "create table yac_test_vector_tab(v1 vector(2,FLOAT32), v2 vector(65535, FLOAT64))", YAC_NULL_TERM_STR));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "insert into yac_test_vector_tab values(:1, :2)", YAC_NULL_TERM_STR));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 1, YAC_PARAM_INPUT, YAC_SQLT_VECTOR, &dim2Vector, -1, sizeof(YacPointer), NULL));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 2, YAC_PARAM_INPUT, YAC_SQLT_VECTOR, &dimMaxVector, -1, sizeof(YacPointer), NULL));

    YAC_CALL(yacExecute(gTestEnv.stmt));
    YAC_CALL(yacCommit(gTestEnv.conn));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "select cosine_distance(v1, :1) from yac_test_vector_tab", YAC_NULL_TERM_STR));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 1, YAC_PARAM_INPUT, YAC_SQLT_VECTOR, &dim2Vector, -1, sizeof(YacPointer), NULL));
    YAC_CALL(yacExecute(gTestEnv.stmt));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "select cosine_distance(v2, :1) from yac_test_vector_tab", YAC_NULL_TERM_STR));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 1, YAC_PARAM_INPUT, YAC_SQLT_VECTOR, &dimMaxVector, -1, sizeof(YacPointer), NULL));
    YAC_CALL(yacExecute(gTestEnv.stmt));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "select V1, V2 from yac_test_vector_tab", YAC_NULL_TERM_STR));    
    YAC_CALL(yacExecute(gTestEnv.stmt));

    YacUint8 colType;
    YAC_CALL(yacColAttribute(gTestEnv.stmt, 0, YAC_COL_ATTR_TYPE, &colType, sizeof(YacUint8), NULL));

    YacUint16 dimension;
    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_VECTOR_DIMENSION, &dimension, sizeof(YacUint16), NULL));
    YacVectorFormat format;
    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_VECTOR_DATA_FORMAT, &format, sizeof(YacVectorFormat), NULL));

    // Test precision and scale for vector type, both should return 0
    YacUint8 precision = 0xFF; // Initialize with non-zero value
    YacInt8 scale = 0x7F;      // Initialize with non-zero value
    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_PRECISION, &precision, sizeof(YacUint8), NULL));
    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_SCALE, &scale, sizeof(YacInt8), NULL));

    YacVector* outputDim2Vector = NULL;
    YAC_CALL(yacDescAlloc2(gTestEnv.env, (YacVoid**)&outputDim2Vector, YAC_DESC_VECTOR));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 0, YAC_SQLT_VECTOR, &outputDim2Vector, -1, NULL));
    YacVector* outputDimMaxVector = NULL;
    YAC_CALL(yacDescAlloc2(gTestEnv.env, (YacVoid**)&outputDimMaxVector, YAC_DESC_VECTOR));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 1, YAC_SQLT_VECTOR, &outputDimMaxVector, -1, NULL));

    YacUint32 fetchedRows;
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    YacChar dim2VecText[8001];
    YacUint32 resultLen = 8000;
    YAC_CALL(yacVectorToText(outputDim2Vector, dim2VecText, &resultLen, 0));

    YacChar* dimMaxVecText = (YacChar*)malloc(MB(32));
    YacUint32 dimMaxVecTextLen = MB(32);
    YAC_CALL(yacVectorToText(outputDimMaxVector, dimMaxVecText, &dimMaxVecTextLen, 0));

    // direct execute
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "select V1, V2 from yac_test_vector_tab", YAC_NULL_TERM_STR));    

    YAC_CALL(yacColAttribute(gTestEnv.stmt, 0, YAC_COL_ATTR_TYPE, &colType, sizeof(YacUint8), NULL));

    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_VECTOR_DIMENSION, &dimension, sizeof(YacUint16), NULL));
    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_VECTOR_DATA_FORMAT, &format, sizeof(YacVectorFormat), NULL));

    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_PRECISION, &precision, sizeof(YacUint8), NULL));
    YAC_CALL(yacColAttribute(gTestEnv.stmt, 1, YAC_COL_ATTR_SCALE, &scale, sizeof(YacInt8), NULL));

    YAC_CALL(yacBindColumn(gTestEnv.stmt, 0, YAC_SQLT_VECTOR, &outputDim2Vector, -1, NULL));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 1, YAC_SQLT_VECTOR, &outputDimMaxVector, -1, NULL));

    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    YAC_CALL(yacVectorToText(outputDim2Vector, dim2VecText, &resultLen, 0));

    YAC_CALL(yacVectorToText(outputDimMaxVector, dimMaxVecText, &dimMaxVecTextLen, 0));

    free(dimMaxVecText);
    free(expectedMaxVecText);
    YAC_CALL(yacDescFree2(gTestEnv.env, (YacVoid**)outputDim2Vector, YAC_DESC_VECTOR));
    YAC_CALL(yacDescFree2(gTestEnv.env, (YacVoid**)outputDimMaxVector, YAC_DESC_VECTOR));
    YAC_CALL(yacDescFree2(gTestEnv.env, (YacVoid**)dimMaxVector, YAC_DESC_VECTOR));
    YAC_CALL(yacDescFree2(gTestEnv.env, (YacVoid**)dim2Vector, YAC_DESC_VECTOR));
    return YAC_SUCCESS;
}

YacResult cexample()
{
    YAC_CALL(testConnect());
    YAC_CALL(testVector());
    YAC_CALL(testDisConnect());

    return YAC_SUCCESS;
}

int main()
{
    if (cexample() == YAC_SUCCESS) {
        printf("cexample succeed!\n");
    } else {
        printError();
        printf("cexample failed!\n");
    }
    return 0;
}

```

## Example with Python Driver (yaspy)


```python
#test_vector.py
"""
The example code for vector columns
Including INSERT, QUERY, UPDATE, and DELETE vector columns for vector columns with yaspy driver
"""

import array
import sys
import os

# Add project path
sys.path.insert(0, os.path.dirname(os.path.abspath(__file__)))

import yaspy

def get_connection():
    """Config the connection information of the database"""
    host = "127.0.0.1"
    port = 1688
    user = "regress"
    passwd = "regress"
    dsn = f"{host}:{port}"
    return yaspy.connect(dsn=dsn, user=user, password=passwd)

def drop_table(cursor, table_name: str):
    """Delete tables"""
    if table_name:
        cursor.execute(f"drop table if exists {table_name}")

def demo_vector_insert(connection, cursor):
    """insert data to vector column"""
    table_name = "demo_vector_insert"
    drop_table(cursor, table_name)

    # Creat a table setting the dimension of the vector with 4
    cursor.execute(f"create table {table_name}(id int, vec vector(4))")

    # Use array.array to create a float32 vector data
    vec_data = array.array("f", [1.0, 2.0, 3.0, 4.0])

    # Insert data
    cursor.execute(f"insert into {table_name} values(?, ?)", [1, vec_data])
    connection.commit()

    print(f"✓ Insert completed: vector data {list(vec_data)} have been inserted into {table_name}")

def demo_vector_select(connection, cursor):
    """Query Vector data"""
    table_name = "demo_vector_insert"  

    # Query Vector data
    cursor.execute(f"select id, vec from {table_name} where id = ?", [1])
    result = cursor.fetchone()

    if result:
        retrieved_vec = result[1]
        print(f"✓ Query completed: id={result[0]}, vec={list(retrieved_vec)}")
    else:
        print("✗ Query failed: Cannot find the data")

def demo_vector_update(connection, cursor):
    """Update Vector data"""
    table_name = "demo_vector_insert"

    # create a vector data
    new_vec = array.array("f", [10.0, 20.0, 30.0, 40.0])

    # Update data
    cursor.execute(f"update {table_name} set vec = ? where id = ?", [new_vec, 1])
    connection.commit()

    # Confirm the result
    cursor.execute(f"select vec from {table_name} where id = ?", [1])
    result = cursor.fetchone()

    if result:
        retrieved_vec = result[0]
        print(f"✓ Update completed: the data has been changed to {list(retrieved_vec)}")

def demo_vector_delete(connection, cursor):
    """Delete Vector data"""
    table_name = "demo_vector_insert"

    # Delete data
    cursor.execute(f"delete from {table_name} where id = ?", [1])
    connection.commit()

    # Confirm the result
    cursor.execute(f"select count(*) from {table_name}")
    result = cursor.fetchone()

    if result[0] == 0:
        print(f"✓ Delete completed: The data in {table_name} have been deleted")
    else:
        print(f"✗ Delete failed: {result[0]} records is remaining")

def demo_vector_with_float64(connection, cursor):
    """Example for float64 vector column"""
    table_name = "demo_vector_float64"
    drop_table(cursor, table_name)

    # Create vector table with Float64 data type
    cursor.execute(f"create table {table_name}(id int, vec vector(4, float64))")

    # Use array.array setting the type as Float64 
    vec_data = array.array("d", [1.5, 2.5, 3.5, 4.5])

    # Insert data
    cursor.execute(f"insert into {table_name} values(?, ?)", [1, vec_data])
    connection.commit()

    # Query data
    cursor.execute(f"select vec from {table_name} where id = ?", [1])
    result = cursor.fetchone()

    if result:
        retrieved_vec = result[0]
        print(f"✓ Float64 config completed: vec={list(retrieved_vec)}")

    # Clear data
    drop_table(cursor, table_name)
    connection.commit()

def demo_vector_multiple_rows(connection, cursor):
    """Example for several rows"""
    table_name = "demo_vector_multi"
    drop_table(cursor, table_name)

    # Create table
    cursor.execute(f"create table {table_name}(id int, vec vector(3))")

    # Insert data
    vectors = [
        array.array("f", [1.0, 2.0, 3.0]),
        array.array("f", [4.0, 5.0, 6.0]),
        array.array("f", [7.0, 8.0, 9.0]),
    ]

    for i, vec in enumerate(vectors, start=1):
        cursor.execute(f"insert into {table_name} values(?, ?)", [i, vec])
    connection.commit()

    # Query all data
    cursor.execute(f"select id, vec from {table_name} order by id")
    results = cursor.fetchall()

    print(f"✓ Query completed: {len(results)} rows in total")
    for row in results:
        print(f"  id={row[0]}, vec={list(row[1])}")

    # Clear data
    drop_table(cursor, table_name)
    connection.commit()

def demo_vector_null(connection, cursor):
    """Example for Null"""
    table_name = "demo_vector_null"
    drop_table(cursor, table_name)

    # Create table
    cursor.execute(f"create table {table_name}(id int, vec vector(3))")

    # Insert NULL
    cursor.execute(f"insert into {table_name} values(?, ?)", [1, None])
    connection.commit()

    # Query NULL
    cursor.execute(f"select vec from {table_name} where id = ?", [1])
    result = cursor.fetchone()

    if result and result[0] is None:
        print("✓ Operation completed: Insert NULL successful")
    else:
        print("✗ Operation fail")

    # Clear data
    drop_table(cursor, table_name)
    connection.commit()

def main():
    print("=" * 60)
    print("Vector example")
    print("=" * 60)

    try:
        # Obtain the database connection
        connection = get_connection()
        cursor = connection.cursor()
        print("✓ Connection successful\n")

        print("--- 1. Insert data ---")
        demo_vector_insert(connection, cursor)

        print("\n--- 2. Select data ---")
        demo_vector_select(connection, cursor)

        print("\n--- 3. Update data ---")
        demo_vector_update(connection, cursor)

        print("\n--- 4. Delete data ---")
        demo_vector_delete(connection, cursor)

        print("\n--- 5. Float64 vector operation ---")
        demo_vector_with_float64(connection, cursor)

        print("\n--- 6. Several vector rows ---")
        demo_vector_multiple_rows(connection, cursor)

        print("\n--- 7. NULL for vector ---")
        demo_vector_null(connection, cursor)

        print("\n" + "=" * 60)
        print("DONE!")
        print("=" * 60)

    except Exception as e:
        print(f"\n✗ ERROR: {e}")
        sys.exit(1)
    finally:
        if 'connection' in locals():
            connection.close()
            print("\n✓ The connection is closed")

if __name__ == "__main__":
    main()
```

Run the application in a Linux environment:

```shell
python3 test_vector.py
```

Run the application in a Windows environment:

```shell
python test_vector.py
```

## ## Example with JDBC Driver 

```java
package quickstart;
import java.sql.*;
import java.util.*;

public class VectorWithPreparedStatementExample {
    public static void main(String[] args) {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        
        try {
            Class.forName("com.yashandb.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            System.out.println("Driver not found: " + e.getMessage());
            return;
        }

        Connection conn = null;
        try {
            conn = DriverManager.getConnection(url, info);
            
            String createTableSql = "CREATE TABLE VECTOR_DATA1 (" +
                    "ID INT PRIMARY KEY," +
                    "C1 VECTOR(4, FLOAT64))";
            try (Statement stmt = conn.createStatement()) {
                stmt.execute(createTableSql);
                System.out.println("Table created or already exists");
            } catch (SQLException e) {
                System.out.println("Create table info: " + e.getMessage());
            }
            
            String sql ="insert into VECTOR_DATA1 values (?,?)";
            try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
                ps.setInt(1,i);
                ps.setObject(2,Vector.ofFloat64Values(new double[]{0.1,0.2,0.3,0.4}));
                ps.executeUpdate();
            } catch (SQLException e) {
                System.out.println("Error in batch processing: " + e.getMessage());
                e.printStackTrace();
            }

            try (Statement statement = conn.createStatement()) {
                
                String sql ="select * from VECTOR_DATA1";
                ResultSet resultSet = statement.executeQuery(sql);
                while (resultSet.next()) {

                    Vector vector = (Vector) resultSet.getObject(2);
                    double[] doublesValue = vector.toDoubleArray();
                    System.out.println(Arrays.toString(doublesValue));// [0.1, 0.2, 0.3, 0.4]

                }
            } catch (SQLException e) {
                System.out.println("Error: " + e.getMessage());
                e.printStackTrace();
            }
            
        } catch (SQLException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```