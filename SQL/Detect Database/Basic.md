# Querying the database type and version

The following are some queries to determine the database version for some popular database types:

Database type                            Query<br>
Microsoft, MySQL--                         `SELECT @@version`<br>
Oracle       --                            `SELECT * FROM v$version`<br>
Postgresql        --                       `SELECT version()`<br>


For example, you could use a `UNION` attack with the following input:

```bash
' UNION SELECT @@version--
```
Payload for Oracle
`' UNION SELECT BANNER, NULL FROM v$version--`
