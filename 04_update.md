### Functions:

### Clauses:

### 1. Inserting a New Record

```sql
/* multi row insert */
insert into dept (deptno,dname,loc)
values (1,'A','B'), (2,'B','C')
```

### 2. Inserting Default Values

In this case, all columns will be set to their default values.

```sql
insert into D values ()
```

### 3. Overriding a Default Value with NULL

```sql
insert into d (id, foo) values (null, 'Brighten')
```

### 4. Copying Rows from One Table into Another

```sql
insert into dept_east (deptno,dname,loc)
select deptno,dname,loc
from dept
where loc in ('NEW YORK','BOSTON')
```

### 5. Copying a Table Definition

Use the CREATE TABLE command with a subquery that returns no rows:

```sql
create table dept_2 as
select * from dept
where 1 = 0
```

### 6. Inserting into Multiple Tables at Once

MySQL, PostgreSQL, and SQL Server: As of the time of this writing, these vendors do not support multitable inserts.

### 7. Blocking Inserts to Certain Columns

Create a view on the table exposing only those columns you want to expose. Then force all inserts to go through that view.
For example, to create a view exposing the three columns in EMP:

```sql
create view new_emps as
select empno, ename, job
from emp
```

### 8. Modifying Records in a Table

```sql
update emp
set sal = sal*1.10
where deptno = 20
```

### 9.  Updating When Corresponding Rows Exist

```sql
update emp
set sal=sal*1.20
where empno in ( select empno from emp_bonus )
```

### 10. Updating with Values from Another Table

Include both EMP and NEW_SAL in the UPDATE clause of the UPDATE statement and join in the WHERE clause:

```sql
update emp e, new_sal ns
set e.sal=ns.sal,
e.comm=ns.sal/2
where e.deptno=ns.deptno
```

### 11. Merging Records

Currently, MySQL does not have a MERGE statement; otherwise, this query should work on any RDBMS in this book, and in a wide number of others.

### 12. Deleting All Records from a Table

```sql
delete from emp
```

### 13. Deleting Specific Records

```sql
delete from emp where deptno = 10
```

### 14. Deleting a Single Record

```sql
delete from emp where empno = 7782
```

### 15. Deleting Referential Integrity Violations

You want to delete records from a table when those records refer to nonexistent records in some other table. For example, some employees are assigned to depart‐ ments that do not exist. You want to delete those employees.

```sql
delete from emp
where not exists (
    select * from dept
    where dept.deptno = emp.deptno
)
```

```sql
delete from emp
where deptno not in (select deptno from dept)
```

### 16. Deleting Duplicate Records

Use a subquery with an aggregate function such as MIN to arbitrarily choose the ID to retain (in this case only the NAME with the smallest value for ID is not deleted):

```sql
delete from dupes
where id not in (select min(id) from dupes group by name)
```

```sql
delete from dupes
where id not in (
    select min(id)
    from (select id,name from dupes) tmp
    group by name
    )
```

### 17. Deleting Records Referenced from Another Table

```sql
delete from emp
where deptno in (
    select deptno
    from dept_accidents
    group by deptno
    having count(*) >= 3)
```