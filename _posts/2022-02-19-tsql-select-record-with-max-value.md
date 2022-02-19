---
layout: post
title: "T-SQLで最大値の行を取得するクエリ"
date: 2022-02-19
tags: t-sql
---

「最大値」ではなく「最大値の行」を取得するクエリのサンプル。

方法としてはとりあえず3つ思いつきました。

- サブクエリでmax関数を使う方法
- 相関サブクエリとnot existsを使う方法
- rank関数を使う方法

わかりやすさとパフォーマンスで選択する感じですかね。

#### サンプルデータ

```sql
drop table if exists #Temp

select *
into #Temp
from (
	values
		(N'a', 1),
		(N'b', 2),
		(N'c', 3),
		(N'd', 2),
		(N'e', 3)
) as Value(Name, Value);

select * from #Temp;
/*
Name	Value
a	1
b	2
c	3
d	2
e	3
*/
```

#### サブクエリでmax関数を使う方法

```sql
select *
from #Temp
where [Value] = (
	select max([Value])
	from #Temp
);
/*
Name	Value
c	3
e	3
*/
```

### 相関サブクエリとnot existsを使う方法

```sql
select *
from #Temp as t1
where not exists(
	select *
	from #Temp as t2
	where t2.Value > t1.Value
);
/*
Name	Value
c	3
e	3
*/
```

#### rank関数を使う方法

```sql
with Cte
as(
	select
		*,
		rank() over (order by [Value] desc) as Rank
	from #Temp
)
select *
from Cte
where Rank = 1;
/*
Name	Value	Rank
c	3	1
e	3	1
*/
```