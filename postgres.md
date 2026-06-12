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

--pg_backend_pid() is your own connection - exclude it so you don't see this diagnostic query as the longest-running one.

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
