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

### 自定义类型
自定义的类型：`type` 关键字
动态类型：`tabel.column%type` 和 `table%rowtype`
``` sql
declare
	--定义记录类型与变量
	type customer_type is record(v_cust_name varchar2(20),v_cust_id number(10));
	v_customer_type_record customer_type;
	-- %type
	type emp_record is record(
	 v_name employees.last_name%type,--意思为v_name 的类型和 employees表的last_name 类型一致
	 v_email employees.email%type,
	 v_salary employees.salary%type,
	 v_job_id employees.job_id%type);	
    v_emp_record emp_record;	
	-- %rowtype 表示变量的类型和表的字段一致
	v_emp_record employees%rowtype;
begin
	-- 赋值
	select last_name,id into v_customer_type_record;
	select last_name, email, salary, job_id into v_emp_record from employees where employee_id = 186;
	select * into v_emp_record from employees where employee_id=185;
end;	
```


### pl/sql 变量与变量赋值
- `:=`操作符
- 用 `select` 语句
- `out` 或者 `in out` 传参。

### 条件判断
```sql
-- IF ... THEN ... ELSIF ... THEN ... ELSE ... END IF;
declare
 v_salary employees.salary%type;
begin
 --通过 select ... into ... 语句为变量赋值
select salary into v_salary
from employees
where employee_id = 150;
dbms_output.put_line('salary: ' || v_salary);
-- 打印变量的值
if v_salary >= 10000 then
 dbms_output.put_line('salary >= 10000');
elsif v_salary >= 5000 then
 dbms_output.put_line('5000 <= salary < 10000');
else
 dbms_output.put_line('salary < 5000');
end if;


-- CASE ... WHEN ... THEN ...ELSE ... END
declare
 v_sal employees.salary%type;
 v_msg varchar2(50);
begin
 select salary into v_sal
 from employees
 where employee_id = 150;
 v_msg :=
	 case trunc(v_sal / 5000)
	 when 0 then 'salary < 5000'
	 when 1 then '5000<= salary < 10000'
	 else 'salary >= 10000'
	 end;
 dbms_output.put_line(v_sal ||','||v_msg);
end;
```

### 循环体
```sql
--LOOP ... EXIT WHEN ... END LOOP
declare
 --初始化条件
 v_i number(3) := 1;
begin
 loop
 --循环体
 dbms_output.put_line(v_i);
--循环条件
 exit when v_i = 100;
--迭代条件
 v_i := v_i + 1;
 end loop;
end;


--WHILE ... LOOP ... END LOOP
declare
 --初始化条件
 v_i number(3) := 1;
begin
 --循环条件
 while v_i <= 100 loop
 --循环体
 dbms_output.put_line(v_i);
 --迭代条件
 v_i := v_i + 1;
 end loop;
end;

-- 第三种
begin
 for i in 1 .. 100 loop
 dbms_output.put_line(i);
 end loop;
end;

-- goto
declare
  --标记值, 若为 1 则是素数, 否则不是
  v_flag number(1) := 0;
begin
  for i in 2 .. 100 loop
    v_flag := 1;
    for j in 2 .. sqrt(i) loop
      if i mod j = 0 then
        v_flag := 0;
        goto label;
      end if;
    end loop;
    <<label>>
    if v_flag = 1 then
      dbms_output.put_line(i);
    end if;
  end loop;
end;

```

### 游标
```sql
declare
	--1. 定义游标
	cursor salary_cursor is select salary from employees where department_id = 80;
	v_salary employees.salary%type;
begin
	--2. 打开游标
	open salary_cursor;
	--3. 提取游标
	fetch salary_cursor into v_salary;
	--4. 对游标进行循环操作: 判断游标中是否有下一条记录
	while salary_cursor%found loop
		 dbms_output.put_line('salary: ' || v_salary);
		 fetch salary_cursor into v_salary;
	end loop;
	--5. 关闭游标
	close salary_cursor;
end;

-- 游标 配合 for 
declare
	cursor emp_cursor is select last_name,email,salary from employees where manager_id = 100;
begin
	for v_emp_record in emp_cursor loop
	dbms_output.put_line(v_emp_record.last_name||','||v_emp_record.email||','||v_emp_record.salary);
	end loop;
end;

-- 带参数游标
declare
  --定义游标
  cursor emp_sal_cursor(dept_id number, sal number) is
    select salary + 1000 sal, employee_id id
      from employees
     where department_id = dept_id 
	   and salary > sal;
  --定义基数变量
  temp number(4, 2);
begin
  --处理游标的循环操作
  for c in emp_sal_cursor(sal => 4000, dept_id => 80) loop
    --判断员工的工资, 执行 update 操作
    --dbms_output.put_line(c.id || ': ' || c.sal);
    if c.sal <= 5000 then
      temp := 0.05;
    elsif c.sal <= 10000 then
      temp := 0.03;
    elsif c.sal <= 15000 then
      temp := 0.02;
    else
      temp := 0.01;
    end if;
    dbms_output.put_line(c.sal || ': ' || c.id || ', ' || temp);
    --update employees set salary = salary * (1 + temp) where employee_id = c.id;
  end loop;
end;
```

### 异常处理

- 预定义异常     
```sql
declare
   v_sal employees.salary%type;
begin
   select salary into v_sal from employees where employee_id >100;
   dbms_output.put_line(v_sal);
exception
   when Too_many_rows then dbms_output.put_line('输出的行数太多了 ');
end;

```

- 非预定义异常
```sql
declare
   v_sal employees.salary%type;
   delete_mgr_excep exception;
   --把自定义的异常和 oracle 的错误关联起来
   PRAGMA EXCEPTION_INIT(delete_mgr_excep，-2292);
begin
   delete from emploees where emploee_id = 100;
   select salary into v_sal from employees where emploee_id > 100;
   dbms_output.put_line(v_sal);
exception
   when Too_many_rows then dbms_output.put_line('输出的行数太多了 ');
   when delete_mgr_excep then dbms_output.put_line()'Manager 不能直接被删除';
end;
```

- 用户自定义异常
```sql
declare
  v_sal employees.salary%type;
  --声明一个异常
  delete_mgr_excep exception;
  --把自定义的异常和 oracle 的错误关联起来
  PRAGMA EXCEPTION_INIT(delete_mgr_excep, -2292);
  --声明一个异常
  too_high_sal exception;
begin
  select salary into v_sal from employees where employee_id =100;
  if v_sal > 1000 then
    raise too_high_sal;
  end if;
  delete from employees where employee_id = 100;
  dbms_output.put_line(v_sal);
exception
  when Too_many_rows then dbms_output.put_line('输出的行数太多了 ');
  when delete_mgr_excep then dbms_output.put_line('Manager 不能直接被删除');
  --处理异常
  when too_high_sal then dbms_output.put_line('工资过高了 ');
end;
```

### 存储函数和过程
存储函数:有返回值，创建完成后，通过 `select function() from dual;`执行
存储过程:由于没有返回值，创建完成后，不能使用 select 语句，只能使用 pl/sql 块执行



	[格式]      
	--函数的声明(有参数的写在小括号里)      
	create or replace function func_name(v_param varchar2)      
	--返回值类型      
	return varchar2      
	is      
	--PL/SQL 块变量、记录类型、游标的声明(类似于前面的 declare 的部分)       
	begin      
	--函数体(可以实现增删改查等操作，返回值需要 return)       
	 return 'helloworld'|| v_param;      
	end;  

```sql
create or replace function sum_sal(dept_id number) return number is
  cursor sal_cursor is 
    select salary from employees where department_id = dept_id;
  v_sum_sal number(8) := 0;
begin
  for c in sal_cursor loop
    v_sum_sal := v_sum_sal + c.salary;
  end loop;
  --dbms_output.put_line('sum salary: ' || v_sum_sal);
  return v_sum_sal;
end;
```	

使用 OUT 型参数实现多个返回值
```sql
create or replace function sum_sal(dept_id number, total_count out number)
  return number is
  cursor sal_cursor is 
    select salary from employees where department_id = dept_id;
  v_sum_sal number(8) := 0;
begin
  total_count := 0;
  for c in sal_cursor loop
    v_sum_sal := v_sum_sal + c.salary;
    total_count := total_count + 1;
  end loop;
  --dbms_output.put_line('sum salary: ' || v_sum_sal);
  return v_sum_sal;
end;
```


存储过程
```sql
create or replace procedure sum_sal_procedure(dept_id number, v_sum_sal out number) is
  cursor sal_cursor is 
    select salary from employees where department_id = dept_id;
begin
  v_sum_sal := 0;
  for c in sal_cursor loop
    --dbms_output.put_line(c.salary);
    v_sum_sal := v_sum_sal + c.salary;
  end loop;
  dbms_output.put_line('sum salary: ' || v_sum_sal);
end;

--[执行]
declare
  v_sum_sal number(10) := 0;
begin
  sum_sal_procedure(80,v_sum_sal);
end;
```

### 触发器
```sql
create or replace trigger hello_trigger
after
update on employees
--for each row
begin
  dbms_output.put_line('hello...');
  --dbms_output.put_line('old.salary:'|| :OLD.salary||',new.salary'||:NEW.salary);
end;
```

在对 my_emp 记录进行删除时，在my_emp_bak 表中备份对应的记录
```sql
--1). 准备工作:
create table my_emp as select employee_id id, last_name name, salary sal from  employees;
create table my_emp_bak as select employee_id id, last_name name, salary sal from employees where 1 = 2
--2).
create or replace trigger bak_emp_trigger
  before delete on my_emp
  for each row
begin
  insert into my_emp_bak values(:old.id, :old.name, :old.sal);
end;
```
### Procedure PL/SQL的调试

1. 找到需要调试的`procedure`
2. 右击选择 **添加调试信息**
3. 找到需要调试的 函数 右击选择 test

```sql
begin
  -- Call the procedure
  pkg_name.procedure_name(
    i_input_param => i_input_value,
    o_output_value => o_output_value
  );
end;
```


### SQL 函数
decode-



# Oracle

oracle 备份表和还原表
```sql
create  table   A0  as select * from  A; -- 备份
...
-- 以下为还原表
delete from A;
insert into A select * from A0;
```

--查询锁
```sql
select t2.username,
       t2.sid,
       t2.serial#,
       t3.object_name,
       t2.osuer,
       t2.machine,
       t2.program,
       t2.login_time,
       t2.command,
       t2.lockwait,
       t2.saddr,
       t2.paddr,
       t2.taddr,
       t2.sql_address,
       t1.locked_mode
    from v$locked_object t1, v$session t2, dba_objects t3
   where t1.session_id = t2.sid
     and t.object_id = t3.object_id
  order by t2.login_time;   
```

杀锁的进程
```sql
alter system kill session 'sid,serial#';
```
