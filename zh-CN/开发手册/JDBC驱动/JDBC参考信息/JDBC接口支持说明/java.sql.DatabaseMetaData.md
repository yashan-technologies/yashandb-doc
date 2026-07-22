## 描述

`java.sql.DatabaseMetaData`接口是与数据库相关的综合信息接口，用户可通过此接口获取并处理底层DBMS的信息。

部分DatabaseMetaData方法以ResultSet对象的形式返回信息列表，可通过常规ResultSet方法（例如getString和getInt）从这些ResultSet对象检索数据。

如果给定形式的元数据不可用，则接口返回空的ResultSet。

返回列可以定义为ResultSet对象之外的其他列，但应该通过JDBC驱动程序定义，并且必须通过其列标签访问。

详细接口说明请参考[JDBC官方网站](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/DatabaseMetaData.html)。

## 方法

YashanDB JDBC驱动对DatabaseMetaData接口有如下方法：

> **Note**: 
>
> 下表中如下方法不适用于存算一体分布式集群部署：
>
> - getProcedures​(String catalog, String schemaPattern, String procedureNamePattern)
> - getProcedureColumns​(String catalog, String schemaPattern, String procedureNamePattern, String columnNamePattern)

| 方法|  返回类型|
| --- | --- |
| allProceduresAreCallable() | boolean |
| allTablesAreSelectable() | boolean |
| autoCommitFailureClosesAllResultSets() | boolean |
| dataDefinitionCausesTransactionCommit() | boolean |
| dataDefinitionIgnoredInTransactions() | boolean |
| deletesAreDetected​(int type) | boolean |
| doesMaxRowSizeIncludeBlobs() | boolean |
| generatedKeyAlwaysReturned() | boolean |
| getColumns​(String catalog, String schemaPattern, String tableNamePattern, String columnNamePattern) | ResultSet |
| getConnection() | Connection |
| getDatabaseMajorVersion() | int |
| getDatabaseMinorVersion() | int |
| getDatabaseProductName() | String |
| getDatabaseProductVersion() | String |
| getDefaultTransactionIsolation() | int |
| getDriverMajorVersion() | int |
| getDriverMinorVersion() | int |
| getDriverName() | String |
| getDriverVersion() | String |
| getExtraNameCharacters() | String |
| getFunctions​(String catalog, String schemaPattern, String functionNamePattern) | ResultSet |
| getIdentifierQuoteString() | String |
| getIndexInfo​(String catalog, String schema, String table, boolean unique, boolean approximate) | ResultSet |
| getJDBCMajorVersion() | int |
| getJDBCMinorVersion() | int |
| getMaxBinaryLiteralLength() | int |
| getMaxCatalogNameLength() | int |
| getMaxCharLiteralLength() | int |
| getMaxColumnNameLength() | int |
| getMaxColumnsInGroupBy() | int |
| getMaxColumnsInIndex() | int |
| getMaxColumnsInOrderBy() | int |
| getMaxColumnsInSelect() | int |
| getMaxColumnsInTable() | int |
| getMaxConnections() | int |
| getMaxCursorNameLength() | int |
| getMaxIndexLength() | int |
| getMaxLogicalLobSize() | long |
| getMaxProcedureNameLength() | int |
| getMaxRowSize() | int |
| getMaxSchemaNameLength() | int |
| getMaxStatementLength() | int |
| getMaxStatements() | int |
| getMaxTableNameLength() | int |
| getMaxTablesInSelect() | int |
| getMaxUserNameLength() | int |
| getNumericFunctions() | String |
| getPrimaryKeys​(String catalog, String schema, String table) | ResultSet |
| getProcedures​(String catalog, String schemaPattern, String procedureNamePattern) | ResultSet |
| getProcedureTerm() | String |
| getResultSetHoldability() | int |
| getRowIdLifetime() | RowIdLifetime |
| getSchemas() | ResultSet |
| getSchemas​(String catalog, String schemaPattern) | ResultSet |
| getSchemaTerm() | String |
| getSearchStringEscape() | String |
| getSQLKeywords() | String |
| getSQLStateType() | int |
| getStringFunctions() | String |
| getSystemFunctions() | String |
| getTables​(String catalog, String schemaPattern, String tableNamePattern, String\[\] types) | ResultSet |
| getTableTypes() | ResultSet |
| getTimeDateFunctions() | String |
| getTypeInfo() | ResultSet |
| getURL() | String |
| getUserName() | String |
| insertsAreDetected​(int type) | boolean |
| isReadOnly() | boolean |
| locatorsUpdateCopy() | boolean |
| nullPlusNonNullIsNull() | boolean |
| nullsAreSortedAtEnd() | boolean |
| nullsAreSortedAtStart() | boolean |
| nullsAreSortedHigh() | boolean |
| nullsAreSortedLow() | boolean |
| othersDeletesAreVisible​(int type) | boolean |
| othersInsertsAreVisible​(int type) | boolean |
| othersUpdatesAreVisible​(int type) | boolean |
| ownDeletesAreVisible​(int type) | boolean |
| ownInsertsAreVisible​(int type) | boolean |
| ownUpdatesAreVisible​(int type) | boolean |
| storesLowerCaseIdentifiers() | boolean |
| storesLowerCaseQuotedIdentifiers() | boolean |
| storesMixedCaseIdentifiers() | boolean |
| storesMixedCaseQuotedIdentifiers() | boolean |
| storesUpperCaseIdentifiers() | boolean |
| storesUpperCaseQuotedIdentifiers() | boolean |
| supportsAlterTableWithAddColumn() | boolean |
| supportsAlterTableWithDropColumn() | boolean |
| supportsANSI92EntryLevelSQL() | boolean |
| supportsANSI92FullSQL() | boolean |
| supportsANSI92IntermediateSQL() | boolean |
| supportsBatchUpdates() | boolean |
| supportsColumnAliasing() | boolean |
| supportsConvert() | boolean |
| supportsConvert​(int fromType, int toType) | boolean |
| supportsCoreSQLGrammar() | boolean |
| supportsCorrelatedSubqueries() | boolean |
| supportsDataDefinitionAndDataManipulationTransactions() | boolean |
| supportsDataManipulationTransactionsOnly() | boolean |
| supportsDifferentTableCorrelationNames() | boolean |
| supportsExpressionsInOrderBy() | boolean |
| supportsExtendedSQLGrammar() | boolean |
| supportsFullOuterJoins() | boolean |
| supportsGetGeneratedKeys() | boolean |
| supportsGroupBy() | boolean |
| supportsGroupByBeyondSelect() | boolean |
| supportsGroupByUnrelated() | boolean |
| supportsIntegrityEnhancementFacility() | boolean |
| supportsLikeEscapeClause() | boolean |
| supportsLimitedOuterJoins() | boolean |
| supportsMinimumSQLGrammar() | boolean |
| supportsMixedCaseIdentifiers() | boolean |
| supportsMixedCaseQuotedIdentifiers() | boolean |
| supportsMultipleOpenResults() | boolean |
| supportsMultipleResultSets() | boolean |
| supportsMultipleTransactions() | boolean |
| supportsNamedParameters() | boolean |
| supportsNonNullableColumns() | boolean |
| supportsOpenCursorsAcrossCommit() | boolean |
| supportsOpenCursorsAcrossRollback() | boolean |
| supportsOpenStatementsAcrossCommit() | boolean |
| supportsOpenStatementsAcrossRollback() | boolean |
| supportsOrderByUnrelated() | boolean |
| supportsOuterJoins() | boolean |
| supportsPositionedDelete() | boolean |
| supportsPositionedUpdate() | boolean |
| supportsRefCursors() | boolean |
| supportsResultSetConcurrency​(int type, int concurrency) | boolean |
| supportsResultSetHoldability​(int holdability) | boolean |
| supportsResultSetType​(int type) | boolean |
| supportsSavepoints() | boolean |
| supportsSchemasInDataManipulation() | boolean |
| supportsSchemasInIndexDefinitions() | boolean |
| supportsSchemasInPrivilegeDefinitions() | boolean |
| supportsSchemasInProcedureCalls() | boolean |
| supportsSchemasInTableDefinitions() | boolean |
| supportsSelectForUpdate() | boolean |
| supportsStatementPooling() | boolean |
| supportsStoredFunctionsUsingCallSyntax() | boolean |
| supportsStoredProcedures() | boolean |
| supportsSubqueriesInComparisons() | boolean |
| supportsSubqueriesInExists() | boolean |
| supportsSubqueriesInIns() | boolean |
| supportsSubqueriesInQuantifieds() | boolean |
| supportsTableCorrelationNames() | boolean |
| supportsTransactionIsolationLevel​(int level) | boolean |
| supportsTransactions() | boolean |
| supportsUnion() | boolean |
| supportsUnionAll() | boolean |
| updatesAreDetected​(int type) | boolean |
| usesLocalFilePerTable() | boolean |
| usesLocalFiles() | boolean |
| getBestRowIdentifier​(String catalog, String schema, String table, int scope, boolean nullable) | ResultSet |
| getCrossReference​(String parentCatalog, String parentSchema, String parentTable, String foreignCatalog, String foreignSchema, String foreignTable) | ResultSet |
| getExportedKeys​(String catalog, String schema, String table) | ResultSet |
| getFunctionColumns​(String catalog, String schemaPattern, String functionNamePattern, String columnNamePattern) | ResultSet |
| getImportedKeys​(String catalog, String schema, String table) | ResultSet |
| getProcedureColumns​(String catalog, String schemaPattern, String procedureNamePattern, String columnNamePattern) | ResultSet |
| getPseudoColumns​(String catalog, String schemaPattern, String tableNamePattern, String columnNamePattern) | ResultSet |
| getTablePrivileges​(String catalog, String schemaPattern, String tableNamePattern) | ResultSet |
| getVersionColumns​(String catalog, String schema, String table) | ResultSet |
| getUDTs​(String catalog, String schemaPattern, String typeNamePattern, int\[\] types) | ResultSet |
| getSuperTypes​(String catalog, String schemaPattern, String typeNamePattern) | ResultSet |

## 示例

```java
package quickstart;

import java.sql.Connection;
import java.sql.DatabaseMetaData;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Properties;

public class TableAnalyzer {

    public static Connection getConnection() throws SQLException {
        String url = "jdbc:yasdb://192.168.1.2:1688/yasdb";
        Properties info = new Properties();
        info.setProperty("user", "sales");
        info.setProperty("password", "sales");
        Class.forName("com.yashandb.jdbc.Driver");
        return DriverManager.getConnection(url, info);
    }

    // 表结构分析工具
    public void analyzeTableStructure(Connection conn, String tableName)
            throws SQLException {
        DatabaseMetaData metaData = conn.getMetaData();

        System.out.println("=== Analysis of Table: " + tableName + " ===");

        // 获取列信息
        System.out.println("\nColumns:");
        try (ResultSet columns = metaData.getColumns(null, null, tableName, "%")) {
            while (columns.next()) {
                String colName = columns.getString("COLUMN_NAME");
                String type = columns.getString("TYPE_NAME");
                int size = columns.getInt("COLUMN_SIZE");
                int nullable = columns.getInt("NULLABLE");
                String isNullable = nullable == 1 ? "NULL" : "NOT NULL";
                String defaultValue = columns.getString("COLUMN_DEF");

                System.out.printf("  %-20s %-15s %-8d %-10s",
                    colName, type, size, isNullable);
                if (defaultValue != null) {
                    System.out.print(" DEFAULT: " + defaultValue);
                }
                System.out.println();
            }
        }

        // 获取主键
        System.out.println("\nPrimary Keys:");
        try (ResultSet pk = metaData.getPrimaryKeys(null, null, tableName)) {
            while (pk.next()) {
                System.out.println("  " + pk.getString("COLUMN_NAME"));
            }
        }

        // 获取外键
        System.out.println("\nForeign Keys:");
        try (ResultSet fk = metaData.getImportedKeys(null, null, tableName)) {
            while (fk.next()) {
                String pkTable = fk.getString("PKTABLE_NAME");
                String pkColumn = fk.getString("PKCOLUMN_NAME");
                String fkColumn = fk.getString("FKCOLUMN_NAME");
                System.out.println("  " + fkColumn + " -> " +
                        pkTable + "." + pkColumn);
            }
        }

        // 获取索引
        System.out.println("\nIndexes:");
        try (ResultSet indexes = metaData.getIndexInfo(null, null, tableName,
                false, false)) {
            while (indexes.next()) {
                String indexName = indexes.getString("INDEX_NAME");
                if (indexName == null) {
                    continue;
                }
                String columnName = indexes.getString("COLUMN_NAME");
                boolean nonUnique = indexes.getBoolean("NON_UNIQUE");
                System.out.println("  " + indexName + " on " + columnName +
                        (nonUnique ? " (non-unique)" : " (unique)"));
            }
        }
    }
}
```

