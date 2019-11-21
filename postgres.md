### Postgres snippetes

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
