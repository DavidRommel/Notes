### Query All Tables in a Database
---
```sql
SELECT name
FROM sqlite_master
WHERE type = 'table';
```

### Query All Columns for a Specific Table
---
```sql
PRAGMA table_info(YourTableName);
```
