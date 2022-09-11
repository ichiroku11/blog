---
layout: post
title: "T-SQL - クライアントのIPアドレスを取得するクエリ"
date: 
tags: t-sql
---

```sql
select *
from sys.dm_exec_connections
where session_id = @@spid;
```

### 参考

- https://docs.microsoft.com/ja-jp/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql?view=sql-server-ver16
- https://docs.microsoft.com/ja-jp/sql/t-sql/functions/spid-transact-sql?view=sql-server-ver16


