---
layout: post
title: "Azure SQL Database - 包含データベースは有効"
date: 
tags: azure-sql-db
---

Azure SQL Databaseでは包含データベースは有効とのこと。

下記ドキュメントのNoteより。

[contained database authentication Server Configuration Option - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-ver16)

```
Contained databases are always enabled for SQL Database and Azure Synapse Analytics and cannot be disabled.

SQL Database と Azure Synapse Analytics の場合、包含データベースは常に有効であり、無効にすることはできません。
```

