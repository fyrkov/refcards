### Postgres snippetes

Maintenance and monitoring
```postgres-psql
--1. Top 5 slow queries (from pg_stat_statements):

select
    left(query, 70)                      as query,
    calls,
    round(mean_exec_time::numeric, 1)    as mean_ms,
    round(total_exec_time::numeric, 1)   as total_ms
from pg_stat_statements
order by total_exec_time desc
limit 5;

--2. Longest-running active session (from pg_stat_activity):

select
    pid,
    now() - query_start  as running_for,
    state,
    left(query, 70)      as query
from pg_stat_activity
where state = 'active'
  and pid <> pg_backend_pid()      -- exclude this query itself
order by query_start
limit 5;

--3. Most-bloated tables (from pg_stat_user_tables):

select
    relname,
    n_live_tup,
    n_dead_tup,
    round(100 * n_dead_tup::numeric / nullif(n_live_tup + n_dead_tup, 0), 1) as dead_pct,
    last_autovacuum
from pg_stat_user_tables
order by n_dead_tup desc
limit 5;
```

PSQL example:
```
psql -d ${db_name} -U ${postgres_user) -h localhost -p 33225
```

Operations with timestamp
```sql
insert into event_store (business_event, due_time)  
values ('activeDirectorySynchronization', CURRENT_TIMESTAMP + INTERVAL '100 minutes');
 ```
Show indexes
```
select * 
from
    pg_indexes
where
    schemaname = 'permission'
order by
    tablename,
    indexname;
```

Show tables:
```
SELECT * FROM pg_catalog.pg_tables WHERE schemaname != 'pg_catalog' AND schemaname != 'information_schema';
```

Create index over coulumn with function applied
```
create index if not exists users_work_email_uppercase_key on permission.users (upper(work_email));
```

Show how much space does index take
```
select pg_size_pretty(pg_relation_size('permission.users_work_email_key'));
```

Postgres merge statement (ver > 9.5)
```
insert into permission.users (id, work_email)
select '02f7af3c-f4be-4498-9967-da2d70907a2b', 'scheduled.update@auto1.com'
on conflict do nothing;
```

Explain plan
```
 explain [analyze] select * from transaction_service.transaction where ...;
```

### Hierarchical query
```
with recursive subdepartments as (
    select id, name, parent_id, status, team, created_by, created_on, last_modified_by, modified_on
    from employee.departments
    where id = '452f8795-140b-436d-8bf8-665d13c44172'
    union
    select e.id, e.name, e.parent_id, e.status, e.team, e.created_by, e.created_on, e.last_modified_by, e.modified_on
    from employee.departments e
    inner join subdepartments sub on sub.id = e.parent_id
) select * from subdepartments;
```

## Notes on physical vs logical replication

Physical = byte-for-byte clone of the entire cluster via raw WAL. Logical = decoded row-level changes for selected tables.

The practical differences:

┌─────────────────────────┬─────────────────────────────────────────────────────┬───────────────────────────────────────────────────────────────────────┐
│                         │                Physical (streaming)                 │                           Logical (pub/sub)                           │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Scope                   │ whole cluster, all databases                        │ selected tables (or all tables) in one database                       │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Standby writable?       │ No - strictly read-only                             │ Yes - subscriber is a normal writable DB                              │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Replicates DDL?         │ Yes - new tables, schema changes flow automatically │ No - schema must exist on subscriber; ALTER TABLE not replicated      │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Cross PG version?       │ No - identical major version required               │ Yes - PG 15 → PG 17 works                                             │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Cross OS/arch?          │ No - same architecture                              │ Yes                                                                   │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Replication unit        │ WAL bytes (physical pages)                          │ logical change events (rows)                                          │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Subscriber a full copy? │ Yes - exact replica                                 │ No - only subscribed tables, can have extra tables/indexes of its own │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Setup complexity        │ higher (basebackup, standby mode)                   │ lower (a few SQL commands)                                            │
├─────────────────────────┼─────────────────────────────────────────────────────┼───────────────────────────────────────────────────────────────────────┤
│ Overhead                │ lower (no decoding)                                 │ higher (WAL decoding + apply)                                         │
└─────────────────────────┴─────────────────────────────────────────────────────┴───────────────────────────────────────────────────────────────────────┘

When to use PHYSICAL:

1. High availability / failover - the #1 use. Standby is a hot spare ready to promote if the primary dies. Identical clone, near-zero data loss with sync replication.
2. Read scaling - offload read-only queries (reports, analytics) to standbys. Your DH read_sql could hit a read replica instead of the write primary.
3. Disaster recovery - a standby in another datacenter/region.
4. You want everything - all tables, all databases, automatically, including future schema changes.

The mental model: "I want a complete, always-current, read-only copy of my whole database for failover or read offload."

When to use LOGICAL:

1. Zero-downtime major-version upgrades - the killer use. Replicate PG 16 → PG 17 (physical can't cross versions), let it catch up, cut over with seconds of downtime. The standard way large shops upgrade.
2. Selective replication - you only need some tables on the target (e.g. ship trades to an analytics DB but not the rest).
3. Consolidation / fan-in - many databases replicate their tables into one central warehouse.
4. Fan-out / distribution - one source distributes specific tables to many targets that each also have their own local data.
5. CDC (Change Data Capture) - Debezium and similar tools use logical replication (logical decoding) to stream row changes into Kafka → your DH pipelines (Labs 2-5). This is the proper "live PG → DH" path we discussed.
6. Different schema/indexes on the target - the subscriber can have extra indexes, different partitioning, additional columns with defaults.

The decisive questions:

- Need the target writable? → logical (physical is read-only).
- Need failover / a full hot spare? → physical.
- Crossing PG versions? → logical (physical can't).
- Need only some tables? → logical (physical is all-or-nothing).
- Need DDL to flow automatically? → physical (logical doesn't replicate schema).
- Feeding a streaming pipeline (Kafka/DH)? → logical decoding / CDC.
