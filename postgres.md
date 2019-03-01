### Postgres snippetes

Operations with timestamp
```sql
insert into event_store (business_event, due_time)  
values ('activeDirectorySynchronization', CURRENT_TIMESTAMP + INTERVAL '100 minutes');
 ```
