## Functionality Introduction

Get the pointer to the string stored in yacString. The return value indicates the pointer to the stored string.

## Function Declaration

```c
YacChar*  yacStringPtr(YacHandle hEnv, const YacString* yacString);
```

## Parameter Description

|Parameter Name |Description |
| ----------------- | --------------- |
| hEnv (IN)         | Environment handle. |
| yacString (IN)    | yacString handle. |