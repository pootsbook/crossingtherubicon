---
layout: post
title: "Ensql: A Small Tool for a Big Job"
---

The [2020 edition][prc] of the Paris Ruby Conference was special for a number of reasons. One of those was meeting a host of like-minded people. The careful curation of a handful of speakers was predominantly geared towards individuals who worked on or used simple tools. For example, Luca Guidi spoke about [Hanami 2.0][h2], Jeremy Evans regaled us with [Roda and Sequel][ras], and I was championing the work of Michel Martens in [Rediscovering Ruby][rr].

I also had the pleasure of meeting [Daniel Fone][df]. Aside from our mutual fascination and interest in ancient manuscripts, it is clear that we are both drawn to simplicity. When exchanging emails not so long ago, he mentioned to me that he had written a new Ruby library for executing SQL against a database.

In Ruby we are well furnished with robust, production database libraries. Think of [ActiveRecord][ar], [Sequel][sq], and [rom][rom] for starters. But I was intrigued. Mr. Fone’s library is [Ensql][en] and offers a straightforward way of executing SQL against a database. I made a mental note and went on my way.

Today, when faced with an issue at work where I needed to collect some data from a read-only database, I immediately thought of Ensql. Pulling in a behemoth like ActiveRecord or a fully-featured library like Sequel would have been overkill. I just needed to execute a couple of SQL queries for which the SQL was already in front of my nose. I didn’t need any mapping, any conveniences, just some raw data.

So I reached for Ensql. Getting started was easy. I wasn’t leaning on a pre-existing database connection via the libraries mentioned above, but if I were, Ensql is designed to pick up any existing connection that you have lying around, rather than needing to configure a new one. I did in fact need a new connection so I reached for the pg gem to handle the PostgreSQL connection and plug it in via the Ensql PostgreSQL Adapter.

```
require 'ensql'
require 'ensql/postgresql_adapter'

Ensql.adapter = Ensql::PostgresAdapter.pool do
  PG.connect ENV['DATABASE_URL']
end
```

Once you have a connection then running a query is as simple as passing an SQL query string to Ensql.

```
query = %{
  SELECT *
  FROM invoices as i
  WHERE i.created_at > TO_TIMESTAMP('2021-01-01 00:00:00', 'YYYY-MM-DD HH24:MI:SS')
  ORDER BY i.created_at ASC;
}

result = Ensql.sql(query).run
```

Ensql handles the transformation of the data retrieved from PostgreSQL into Ruby. This then makes it far more convenient than the pg gem without the unwanted baggage of an ORM.

It has adapters for pg, ActiveRecord and Sequel. You can pass in your SQL directly from a Ruby string or you can point Ensql to a file containing a query and it will load it and execute it. It handles errors, transactions, and if necessary, a pool of database connections.

The code is incredibly well documented, the README is thorough, and there is very little code:

| Class/Method/File | SLOC |
| ------- | ------- |
| `Ensql` | 46 |
| `Ensql::Sql` | 139  |
| `Ensql::load_sql` | 33 |
| `Ensql::transaction` | 54 |
| `Ensql::Error` | 5 |
| `Ensql::PoolWrapper` | 19 |
| `Ensql::Adapter` | 109 |
| `Ensql::ActiveRecordAdapter` | 88 |
| `Ensql::PostgresAdapter` | 154 |
| `Ensql::SequelAdapter` | 89 |
| Total | 736 |

It does one job, and it does it well. Five stars. Would use again.

—*Friday 19th March 2021.*

[prc]: https://2020.rubyparis.org
[h2]: https://www.youtube.com/watch?v=7DABLhbNy0I
[ras]: https://www.youtube.com/watch?v=sUBgL_FYUlM
[rr]: https://www.youtube.com/watch?v=s5A4recPPC4
[df]: https://daniel.fone.net.nz
[ar]: https://github.com/rails/rails/tree/main/activerecord
[sq]: http://sequel.jeremyevans.net
[rom]: https://github.com/rom-rb/rom
[en]: https://github.com/danielfone/ensql
