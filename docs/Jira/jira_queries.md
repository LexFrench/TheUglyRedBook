# Useful Jira Query Snippets

### Open tickets that haven't been updated in N days

Query for all Open tickets in given projects that have not been updated (touched in anyway) in more than N days (365 in this case)

```sql
project in (X, Y, Z) AND updated < -365d AND status not in (Closed, Resolved) ORDER BY updated DESC
```

### Query all tickets which are linked to tickets in another project

```sql
```sql
project in (X, Y) AND issueFunction in linkedIssuesOf("project = Z")
```
