---
layout: post
title: "EF Core - GroupJoinメソッドで外部結合（leff outer join）する"
date: 2022-03-29
tags: efcore t-sql
---

2026/01/29 追記

EF Core 10よりLeftJoinメソッドがサポートされるようになりました。以下参照ください。

[EF Core - LefJoinメソッドで外部結合（leff outer join）する &#124; いちろぐ](https://ichiroku11.github.io/blog/2026/01/29/efcore-leftjoin.html)

---

EF CoreでGroupJoinメソッドを使って外部結合するサンプルです。

### サンプルのテーブルとデータ

取得したいサンプルデータです。OuterとInnerを外部結合したいとします。

```sql
use Test;

drop table if exists dbo.[Outer];
drop table if exists dbo.[Inner];

create table dbo.[Outer](
	Id int not null,
	Value nvarchar(10) not null,
	constraint PK_Outer primary key(Id)
);

create table dbo.[Inner](
	Id int not null,
	Value nvarchar(10) not null,
	constraint PK_Inner primary key(Id)
);

insert into dbo.[Outer](Id, Value)
output inserted.*
values
	(1, N'a'),
	(2, N'b'),
	(3, N'c');
/*
Id	Value
1	a
2	b
3	c
*/

insert into dbo.[Inner](Id, Value)
output inserted.*
values
	(1, N'A'),
	(4, N'D');
/*
Id	Value
1	A
4	D
*/

-- GroupJoinを使って実行したいクエリ
select *
from dbo.[Outer]
	left outer join dbo.[Inner]
		on dbo.[Outer].Id = dbo.[Inner].Id
order by dbo.[Outer].Id;
/*
Id	Value	Id	Value
1	a	1	A
2	b	NULL	NULL
3	c	NULL	NULL
*/
```

### GroupJoinメソッドを使って外部結合する

EF CoreでGroupJoinメソッドを使ってデータを取得してみます。

```csharp
using Microsoft.Data.SqlClient;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Logging;

using var context = new AppDbContext();

// GroupJoinを使って外部結合する
var samples = await context.Outers
	.GroupJoin(
		context.Inners,
		outer => outer.Id,
		inner => inner.Id,
		(outer, inners) => new {
			Outer = outer,
			Inners = inners,
		})
	.SelectMany(
		item => item.Inners.DefaultIfEmpty(),
		(item, inner) => new { Outer = item.Outer, Inner = inner })
	.ToListAsync();
// 実行されるSQL
/*
SELECT [o].[Id], [o].[Value], [i].[Id], [i].[Value]
FROM [Outer] AS [o]
LEFT JOIN [Inner] AS [i] ON [o].[Id] = [i].[Id]
*/

Console.WriteLine($"{nameof(Outer.Id)}\t{nameof(Outer.Value)}\t{nameof(Inner.Id)}\t{nameof(Inner.Value)}");
foreach (var entry in samples) {
	Console.WriteLine($"{entry.Outer.Id}\t{entry.Outer.Value}\t{entry.Inner?.Id}\t{entry.Inner?.Value}");
}
// 出力結果
/*
Id	Value	Id	Value
1	a	1	A
2	b
3	c
*/

public record Outer(int Id, string Value);
public record Inner(int Id, string Value);

public class AppDbContext : DbContext {
	public AppDbContext() {
		ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;
	}

	public DbSet<Outer> Outers => Set<Outer>();
	public DbSet<Inner> Inners => Set<Inner>();

	protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder) {
		var connectionString = new SqlConnectionStringBuilder {
			DataSource = ".",
			InitialCatalog = "Test",
			IntegratedSecurity = true,
		}.ToString();
		optionsBuilder.UseSqlServer(connectionString);

		optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
	}

	protected override void OnModelCreating(ModelBuilder modelBuilder) {
		modelBuilder.Entity<Outer>().ToTable(nameof(Outer));
		modelBuilder.Entity<Inner>().ToTable(nameof(Inner));
	}
}
```
