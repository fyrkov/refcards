### Postgres snippetes

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
