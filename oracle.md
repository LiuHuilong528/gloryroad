# PL/SQL
## Block

    DECLARE    -- Declarative part (optional)    
     -- Declarations of local types, variables, & subprograms                 

    BEGIN      -- Executable part (required)      
     -- Statements (which can use items declared in declarative part)    

    [EXCEPTION -- Exception-handling part (optional)    
     -- Exception handlers for exceptions raised in executable part]   
    END;    
 
pl/sql块声明结构。

### pl/sql 变量与变量赋值
- `:=`操作符
- 用 `select` 语句
- `out` 或者 `in out` 传参。

# Oracle

oracle 备份表和还原表
```sql
create  table   A0  as select * from  A; -- 备份
...
-- 以下为还原表
delete from A;
insert into A select * from A0;
```