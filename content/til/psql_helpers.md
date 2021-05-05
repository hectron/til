---
title: psql watch + auto extend
date: 2021-05-04
summary: >
  Let's talk about auto extending result sets
  and watching the last query.
tags:
  - misc
  - postgres
  - db
  - psql
---

# tl; dr

(In `psql`)
```psql
-- to auto-extend results

\x auto

-- to re-run a query every second
\watch 1
```

## Auto-extending results

The `\x` flag in psql is used to change the way results are displayed from a
query.

Often times it's helpful to use `\x` to enable extended mode. This mode allows
you to view columns vertically.

With `\x` disabled, results are displayed as:

```sql
| id | name  |
| 1  | reily |
| 2  | alex  |
```

With `\x` enabled, results are displayed as:

```sql
-------------
id: 1
name: reily
-------------
id: 2
name: alex
-------------
```

With `\x auto`, results are adjusted to your screensize. If your screen is not
wide enough to view the first result, it displays the results as the second.

## Re-running a query

When you run a query, you can also have that query be re-run for you automatically by leveraging the [`\watch`](https://www.postgresql.org/docs/current/app-psql.html) command on a psql session.

You must first run the query you'd like, and then invoke the watch command.

```psql
select * from my_table;

-- re-run this query every 1 second
\watch 1
```
