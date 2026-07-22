|Interface Name |Interface Description |
|--------------------|--------------------------------------|
| [yacDirectExecute](yacDirectExecute) | A request to execute SQL without binding data or binding parameter data initiated by the client |
| [yacExecute](yacExecute)           | A request to execute precompiled SQL with yacPrepare initiated by the client |
| [yacPrepare](yacPrepare)           | A request to prepare SQL initiated by the client          |
| [yacNumResultCols](yacNumResultCols) | Get the number of columns in the statement result set    |
| [yacFetch](yacFetch)               | A request to fetch result sets initiated by the client    |
| [yacNumParams](yacNumParams)       | Get the number of parameters in the statement             |
| [yacStmtGetNextResult](yacStmtGetNextResult) | Get implicit result sets                             |
| [yacGetData](yacGetData)           | Used for the client to retrieve data from the buffered result set |