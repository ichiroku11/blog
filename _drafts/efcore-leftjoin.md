---
layout: post
title: "EF Core - LefJoinメソッドで外部結合（leff outer join）する"
date: 
tags: efcore t-sql
---

EF CoreでLefJoinメソッドを使って外部結合できるようになったので、以前書いたGroupJoinを使ったサンプルを書き直してみました。

[EF Core - GroupJoinメソッドで外部結合（leff outer join）する &#124; いちろぐ](https://ichiroku11.github.io/blog/2022/03/29/efcore-groupjoin.html)

### サンプルのテーブルとデータ

取得したいサンプルデータです。以前と同じデータ。OuterとInnerを外部結合したいとします。

```sql
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

-- 実行したいクエリ
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

### LeftJoinメソッドを使って外部結合する

EF CoreでLeftJoinメソッドを使ってデータを取得してみます。

```csharp
using Microsoft.Data.SqlClient;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Logging;

using var context = new AppDbContext();

// LeftJoinを使って外部結合する
var samples = await context.Outers
	.LeftJoin(
		context.Inners,
		outer => outer.Id,
		inner => inner.Id,
		(outer, inner) => new { Outer = outer, Inner = inner })
	// 以前はGroupJoinを使って以下のように書いていた
	/*
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
		(item, inner) => new { item.Outer, Inner = inner })
	*/
	.OrderBy(item => item.Outer.Id)
	.ToListAsync();
// 実行されるSQL
/*
SELECT [o].[Id], [o].[Value], [i].[Id], [i].[Value]
FROM [Outer] AS [o]
LEFT JOIN [Inner] AS [i] ON [o].[Id] = [i].[Id]
ORDER BY [o].[Id]
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
			TrustServerCertificate = true,
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

### 参考

- https://learn.microsoft.com/ja-jp/ef/core/what-is-new/ef-core-10.0/whatsnew#support-for-the-net-10-leftjoin-and-rightjoin-operators
