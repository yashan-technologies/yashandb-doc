The C driver vector functionality is implemented through the VECTOR data type, which supports the storage and retrieval of vector data. The VECTOR data type is used to store vector data, such as floating-point vector arrays, and supports operations like vector similarity calculation. In the database, the VECTOR type is used to store fixed-length or variable-length floating-point vectors.

The C driver provides a set of API functions to perform operations related to VECTOR data types, including allocating and freeing vectors, constructing vectors from text or arrays, converting vectors to text or arrays, and obtaining vector metadata (dimension, format), etc.

|Interface Name |Interface Description |
|-------------------|--------------------------------------|
| [yacDescAlloc2](yacDescAlloc2) | Allocate space for vector descriptor |
| [yacDescFree2](yacDescFree2) | Free space for vector descriptor |
| [yacVectorFromText](yacVectorFromText) | Construct yacVector from text |
| [yacVectorFromArray](yacVectorFromArray) | Construct yacVector from array |
| [yacVectorToText](yacVectorToText) | Convert yacVector to text |
| [yacVectorToArray](yacVectorToArray) | Convert yacVector to array |
| [yacVectorGetDimension](yacVectorGetDimension) | Get the dimension of the vector in yacVector |
| [yacVectorGetFormat](yacVectorGetFormat) | Get the value type of the vector in yacVector |
