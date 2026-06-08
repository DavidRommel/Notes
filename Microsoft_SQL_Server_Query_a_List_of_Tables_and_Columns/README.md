### Query All Tables in a Database
---
#### Using System Views (Recommended for Performance)
```sql
SELECT 
    SCHEMA_NAME(schema_id) AS [Schema],
    name AS [TableName]
FROM sys.tables
ORDER BY [Schema], [TableName];
```

#### Using Information Schema (Database Standard)
```sql
SELECT 
    TABLE_SCHEMA AS [Schema],
    TABLE_NAME AS [TableName]
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_TYPE = 'BASE TABLE'
ORDER BY [Schema], [TableName];
```

### Query All Columns for a Specific Table
---
#### Using Information Schema
```sql
SELECT 
    COLUMN_NAME AS [ColumnName],
    DATA_TYPE AS [DataType],
    CHARACTER_MAXIMUM_LENGTH AS [MaxLength],
    IS_NULLABLE AS [IsNullable]
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'YourTableName'
ORDER BY ORDINAL_POSITION;
```

#### Using Built-in Stored Procedure
```sql
EXEC sp_help 'YourTableName';
```
