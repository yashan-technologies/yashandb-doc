The C driver of YashanDB supports external memory management by providing methods to manage memory contexts through related callback functions, which supports memory injection.

## Interface

|Function |Description |
| ------------ | -------------------- |
| YacMalocFunc      | Memory allocation callback function      |
| YacRalocFunc      | Memory reallocation callback function     |
| YacMfreeFunc      | Memory deallocation callback function     |

## Structure

```c
/* YACLI mem callbacks */
typedef YacPointer (*YacMalocFunc)(YacPointer ctxp, size_t size);

typedef YacPointer (*YacRalocFunc)(YacPointer ctxp, YacPointer memptr, size_t newSize);

typedef YacVoid (*YacMfreeFunc)(YacPointer ctxp, YacPointer memptr);
 
YacResult yacAllocEnvWithMemCb(YacHandle* env, YacPointer ctxp, YacMalocFunc malocFp, YacRalocFunc ralocFp, YacMfreeFunc mfreeFp);
```

The related parameters are shown in the table below.

|Parameter Name |Description |
| ----- | ---------------- |
| env             | Environment handle    |
| ctxp            | Context pointer       |
| malocFp        | Memory allocation callback function    |
| ralocFp       | Memory reallocation callback function  |
| mfreeFp       | Memory deallocation callback function   |

## Usage Example

### Example 1

```c
static YacPointer testYacMalocFunc(YacPointer ctxp, size_t size)
{
    YacUint32* memCtx = (YacUint32*)ctxp;
    COD_ASSERT(*memCtx == 1); // Ensure the passed memory context value is 1, perform assertion check
    YacPointer buf = malloc(size);
    return buf;
} // Define a test memory allocation function to replace the standard library's malloc function
 
static YacPointer testYacRalocFunc(YacPointer ctxp, YacPointer memptr, size_t newSize)
{
    YacUint32* memCtx = (YacUint32*)ctxp;
    COD_ASSERT(*memCtx == 1);
    YacPointer buf = realloc(memptr, newSize);
    return buf;
} // Define a test memory reallocation function to replace the standard library's realloc function
 
static YacVoid testYacMfreeFunc(YacPointer ctxp, YacPointer memptr)
{
    YacUint32* memCtx = (YacUint32*)ctxp;
    COD_ASSERT(*memCtx == 1);
    free(memptr);
} // Define a test memory deallocation function to replace the standard library's free function
 
TEST_F(TestYacDriverBase, testYacMemCallback)
{
    YacUint32 memCtx = 1; // Create a memory context value for context checking
 
    YacHandle env = NULL;
    YacHandle conn = NULL;
    YacHandle stmt = NULL;
    YAC_EXPECT_CALL(yacAllocEnvWithMemCb(&env, &memCtx, testYacMalocFunc, testYacRalocFunc, testYacMfreeFunc)); // Use memory callback environment allocation function to allocate environment and pass memory context and related memory callback functions
    YAC_EXPECT_CALL(yacAllocHandle(YAC_HANDLE_DBC, env, &conn)); // Allocate conn
    YAC_EXPECT_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR)); // Connect to the database server
 
    YAC_EXPECT_CALL(yacAllocHandle(YAC_HANDLE_STMT, conn, &stmt)); // Allocate stmt
    YAC_EXPECT_CALL(yacDirectExecute(stmt, "select 1 from dual", YAC_NULL_TERM_STR)); // Execute database statement
    YAC_EXPECT_CALL(yacFreeHandle(YAC_HANDLE_STMT, stmt));
 
    yacDisconnect(conn);
    YAC_EXPECT_CALL(yacFreeHandle(YAC_HANDLE_DBC, conn));
    YAC_EXPECT_CALL(yacFreeHandle(YAC_HANDLE_ENV, env));
} // Free stmt, conn, env
```

### Example 2

```c
typedef struct StMemCb {
    YacChar*  buf; // cache, used for memory allocation and deallocation
    YacUint32 bufSize; // total size of cache
    YacUint32 offset; // current offset, used to track the size of allocated memory
} MemCb; // Define a memory callback structure MemCb
 
static YacPointer testYacMalocFunc2(YacPointer ctxp, size_t size)
{
    MemCb* memCtx = (MemCb*)ctxp;
    if (size > memCtx->bufSize - memCtx->offset) {
        return NULL; // Check if there is enough space to allocate the required size of memory
    }
    YacPointer buf = memCtx->buf + memCtx->offset;
    memCtx->offset += size;
    return buf;
} // Define a test memory allocation function to replace the standard library's malloc function
 
static YacPointer testYacRalocFunc2(YacPointer ctxp, YacPointer memptr, size_t newSize)
{
    MemCb* memCtx = (MemCb*)ctxp;
    if (newSize < memCtx->bufSize - memCtx->offset) {
        return NULL;
    } // Check if there is enough space to allocate the required size of memory
    YacPointer buf = memCtx->buf + memCtx->offset;
    memCtx->offset += newSize;
    return buf;
} // Define a test memory reallocation function to replace the standard library's realloc function
 
static YacVoid testYacMfreeFunc2(YacPointer ctxp, YacPointer memptr)
{
    return; // In this example, simply for testing, no actual memory deallocation operation performed
} // Define a test memory deallocation function to replace the standard library's free function
 
TEST_F(TestYacDriverBase, testYacMemCallback2) // Test the case of using custom memory callback functions
{
    MemCb memCb;
    memCb.buf = (YacChar*)malloc(10 << 20); // Allocate 10MB of memory cache
    COD_ASSERT(memCb.buf != NULL); // Check if memory allocation is successful
    memCb.bufSize = 10 << 20; // Set total size of cache
    memCb.offset = 0; // Initialize offset to 0
 
    YacHandle env = NULL;
    YacHandle conn = NULL;
    YacHandle stmt = NULL;
    YAC_EXPECT_CALL(yacAllocEnvWithMemCb(&env, &memCb, testYacMalocFunc2, testYacRalocFunc2, testYacMfreeFunc2));
    YAC_EXPECT_CALL(yacAllocHandle(YAC_HANDLE_DBC, env, &conn));
    YAC_EXPECT_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
 
    YAC_EXPECT_CALL(yacAllocHandle(YAC_HANDLE_STMT, conn, &stmt));
    YAC_EXPECT_CALL(yacDirectExecute(stmt, "select 1 from dual", YAC_NULL_TERM_STR));
    YAC_EXPECT_CALL(yacFreeHandle(YAC_HANDLE_STMT, stmt));
 
    yacDisconnect(conn);
    YAC_EXPECT_CALL(yacFreeHandle(YAC_HANDLE_DBC, conn));
    YAC_EXPECT_CALL(yacFreeHandle(YAC_HANDLE_ENV, env)); // Same as example one
 
    free(memCb.buf); // Free allocated memory cache
}
```
