## Description

`java.sql.DatabaseMetaData` represents that it is an interface for comprehensive information related to databases. Users can obtain and process information about the underlying DBMS through this interface.

Some methods of DatabaseMetaData return a list of information in the form of ResultSet objects, from which data can be retrieved using common ResultSet methods (such as getString and getInt).

If the requested form of metadata is not available, the interface returns an empty ResultSet.

The returned columns can be defined as other columns outside of the ResultSet object, but they must be defined through the JDBC driver program and must be accessed via their column labels.

Detailed interface specifications, please refer to [The JDBC Official Website](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/DatabaseMetaData.html).

## Method

The YashanDB JDBC driver has the following methods for the DatabaseMetaData interface:

> **Note**: 
>
> The following methods in the table are not applicable for ISC Distributed Cluster Deployment:
>
> - getProcedures​(String catalog, String schemaPattern, String procedureNamePattern)
> - getProcedureColumns​(String catalog, String schemaPattern, String procedureNamePattern, String columnNamePattern)

|Method |Return Type |
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

## Example

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

    // Table Structure Analysis Tool
    public void analyzeTableStructure(Connection conn, String tableName)
            throws SQLException {
        DatabaseMetaData metaData = conn.getMetaData();

        System.out.println("=== Analysis of Table: " + tableName + " ===");

        // Get column information
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

        // Get primary keys
        System.out.println("\nPrimary Keys:");
        try (ResultSet pk = metaData.getPrimaryKeys(null, null, tableName)) {
            while (pk.next()) {
                System.out.println("  " + pk.getString("COLUMN_NAME"));
            }
        }

        // Get foreign keys
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

        // Get indexes
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

