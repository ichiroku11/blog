---
layout: post
title: "EF Core - "
date: 
tags: efcore t-sql
---

https://docs.microsoft.com/ja-jp/ef/core/performance/efficient-updating

こちらの記事を見つけたので、複数データを登録するときにINSERT文が1つにまとめられる場合があることを確認してみました。

```csharp
using Microsoft.Data.SqlClient;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Logging;

using var context = new AppDbContext();

context.Database.ExecuteSqlRaw(@"
drop table if exists dbo.Sample;
create table dbo.Sample(
	Id int not null,
	Name nvarchar(5) not null,
	constraint PK_Sample primary key(Id)
);");

context.Samples.Add(new Sample(1, "a"));
context.Samples.Add(new Sample(2, "b"));
context.Samples.Add(new Sample(3, "c"));
await context.SaveChangesAsync();
// 次のINSERT文が3回実行される
/*
INSERT INTO[Sample] ([Id], [Name])
VALUES(@p0, @p1);
*/

context.Samples.AddRange(
	new Sample(4, "d"),
	new Sample(5, "e"),
	new Sample(6, "f"));
await context.SaveChangesAsync();
// 次のINSERT文が3回実行される
/*
INSERT INTO[Sample] ([Id], [Name])
VALUES(@p0, @p1);
*/

context.Samples.Add(new Sample(11, "A"));
context.Samples.Add(new Sample(12, "B"));
context.Samples.Add(new Sample(13, "C"));
context.Samples.Add(new Sample(14, "D"));
await context.SaveChangesAsync();
// 次のINSERT文が1回実行される
/*
INSERT INTO [Sample] ([Id], [Name])
VALUES (@p0, @p1),
	(@p2, @p3),
	(@p4, @p5),
	(@p6, @p7);
*/

context.Samples.AddRange(
	new Sample(15, "E"),
	new Sample(16, "F"),
	new Sample(17, "G"),
	new Sample(18, "H"));
await context.SaveChangesAsync();
// 次のINSERT文が1回実行される
/*
INSERT INTO [Sample] ([Id], [Name])
VALUES (@p0, @p1),
	(@p2, @p3),
	(@p4, @p5),
	(@p6, @p7);
*/

public record Sample(int Id, string Name);

public class AppDbContext : DbContext {
	public AppDbContext() {
		ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;
	}

	public DbSet<Sample> Samples => Set<Sample>();

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
		modelBuilder.Entity<Sample>().ToTable(nameof(Sample));
	}
}
```

