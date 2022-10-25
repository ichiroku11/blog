---
layout: post
title: "T-SQL - 指定したユーザーのロールを取得するクエリ"
date: 
tags: t-sql
---

```sql
select
    dp1.name as [user],
    dp2.name as [role]
from sys.database_principals as dp1
    inner join sys.database_role_members as drm
        on dp1.principal_id = drm.member_principal_id
    inner join sys.database_principals as dp2
        on drm.role_principal_id = dp2.principal_id
where
    -- ユーザー"dbo"のロールを確認する
    dp1.name = 'dbo'
```

// todo: 
- https://docs.microsoft.com/ja-jp/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver16
- https://learn.microsoft.com/ja-jp/sql/relational-databases/system-catalog-views/sys-database-role-members-transact-sql?view=sql-server-ver16

