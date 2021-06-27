### Functions:

* concat()
* case when <sth> then <sth> else <sth> end as <name>
* rand()
* coalesce()

### 1. Retrieving All Rows and Columns from a Table

```sql
select * from emp;
```

### 2. Retrieving a Subset of Rows from a Table

```sql
select * from emp
where deptno = 10;
```

### 3. Finding Rows That Satisfy Multiple Conditions

* The DEPTNO is 10
* The COMM is not NULL
* The salary is $2,000 or less for any employee in DEPTNO 20.

```sql
select * from emp
where deptno = 10
or comm is not null
or sal < 2000 and deptno = 20;
```

### 4. Retrieving a Subset of Columns from a Table

```sql
select ename, deptno, sal
from emp;
```

### 5. Providing Meaningful Names for Columns

```sql
select sal as salary, comm as commission
from emp;
```

### 6. Referencing an Aliased Column in the WHERE Clause

```sql
select *
from (
    select sal as salary, comm as commission from emp
    ) x
where x.salary < 5000;
```

### 7. Concatenating Column Values

```sql
select concat(ename, ' WORKS AS A ',job) as msg
from emp
where deptno=10
```

### 8. Using Conditional Logic in a SELECT Statement

```sql
select ename, sal,
case when sal <= 2000 then 'UNDERPAID'
     when sal >= 4000 then 'OVERPAID'
     else 'OK'
     end as status
from emp
```

### 9. Limiting the Number of Rows Returned

```sql
select *
from emp limit 5
```

### 10. Returning n Random Records from a Table

```sql
select ename, job
from emp
order by rand() limit 5
```

### 11. Finding Null Values

```sql
select *
from emp
where comm is null
```

### 12. Transforming Nulls into Real Values

```sql
select coalesce(comm,0)
from emp
```

### 13. Searching for Patterns

When used in a LIKE pattern-match operation, the percent (%) operator matches any sequence of characters. Most SQL implementations also provide the underscore (“_”) operator to match a single character. By enclosing the search pattern “I” with % operators, any string that contains an “I” (at any position) will be returned. If you do not enclose the search pattern with %, then where you place the operator will affect the results of the query. For example, to find job titles that end in “ER,” prefix the % operator to “ER”; if the requirement is to search for all job titles beginning with “ER,” then append the % operator to “ER.”

Of the employees in departments 10 and 20, you want to return only those that have either an “I” somewhere in their name or a job title ending with “ER”:

```sql
select ename, job
from emp
where deptno in (10,20)
and (ename like '%I%' or job like '%ER')
```