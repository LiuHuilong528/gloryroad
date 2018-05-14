# Oracle

oracle 备份表和还原表
```sql
create  table   A0  as select * from  A; -- 备份
...
-- 以下为还原表
delete from A;
insert into A select * from A0;
```
