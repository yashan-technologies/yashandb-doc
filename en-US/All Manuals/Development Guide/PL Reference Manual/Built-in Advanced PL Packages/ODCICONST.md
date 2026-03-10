The ODCICONST package defines a series of constants suitable for PL programs.

## Related Constants

|Constant |Value |Constant |Value |
| -------- | ---- | -------- | ------- |
|Success   |0 | AlterIndexNone |  0 |
|Error    |1 | AlterIndexRename |  1 |
|Warning  | 2 | AlterIndexRebuild |  2 |
| ErrContinue | 3 | AlterIndexRebuildOnline | 3 |
| Fatal |4 | AlterIndexModifyCol | 4 |
| PredExactMatch  | 1 | AlterIndexUpdBlockRefs |  5 |
| PredPrefixMatch | 2 | AlterIndexRenameCol | 6 |
| PredIncludeStart| 4 | AlterIndexRenameTab | 7 |
| PredIncludeStop | 8 | AlterIndexMigrate | 8 |
| PredObjectFunc | 16 | RangePartn | 2 |
| PredObjectPkg  | 32 | HashPartn | 4 |
| PredObjectType | 64 | Parallel | 16 |
| PredMultiTable | 128 | Unusable | 32 |
| PredNotEqual   | 256 | IndexOnIOT | 64 |
| QueryFirstRows | 1 | TransTblspc | 128 |
| QueryAllRows   | 2 | FunctionIdx | 256 |
| QuerySortAsc   | 4 | ListPartn | 512 |
| QuerySortDesc  | 8 | UpdateGlobalIndexes | 1024 |
| QueryBlocking  | 16| RefPartn | 2048 |
| CleanupCall    | 1 | CompPartn |  8192 |
| RegularCall    | 2 | SubPartn | 16384 |
| ObjectFunc     | 1 | DefaultDegree | 32767 |
| ObjectPkg      | 2 | DebuggingOn | 1 |
| ObjectType     | 4 | NoData | 2 |
| ArgOther       | 1 | UserParamString | 4 |
| ArgCol         | 2 | RowMigration | 8 |
| ArgLit         | 3 | IndexKeyChanged |16 |
| ArgAttr        | 4 | FirstCall | 1 |
| ArgNull        | 5 | IntermediateCall | 2 |
| ArgCursor      | 6 | FinalCall | 3 |
| PercentOption  | 1 | RebuildIndex | 4 |
| RowOption      | 2 | RebuildPMO | 5 |
| EstimateStats  | 1 | StatsGlobal | 6 |
| ComputeStats   | 2 | StatsGlobalAndPartition | 7 |
| StatsPartition | 8 | CompFilterByCol | 1 |
| FetchOp        | 1 | CompOrderByCol  | 2 |
| PopulateOp     | 2 | CompOrderDscCol | 4 |
| Sample         | 1 | CompUpdatedCol | 8 |
| SampleBlock    | 2 | CompRenamedCol | 16 |
| QueryCoordinator | 1 | CompRenamedTopADT | 32 |
| Shadow           | 2 | CompReferencedCol | 64 |
| Slave            | 4 | ColumnExpr | 1 |
| FetchEOS         | 1 | AncOpExpr | 2 |
| SortAsc          | 1 | NullsFirst | 4 |
| SortDesc         | 2 | AddPartition | 1 |
| DropPartition    | 2 |

***Example***

```plsql
CREATE OR REPLACE FUNCTION TEST_FUNC(A IN INT) RETURN INT AS
BEGIN
    IF A > 10 THEN
        RETURN ODCICONST.SUCCESS;
    ELSE
        RETURN ODCICONST.ERROR;
    END IF;
END;
/

--result
SELECT TEST_FUNC(100) FROM DUAL;

 TEST_FUNC(100) 
------------ 
           0
```
      
