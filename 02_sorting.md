### Functions:

* `substr(string, start, length)`: if length is omitted, the whole string will be returned (from the start position).

### Clauses:

* order by <col> asc/desc
* order by case when

### 1. Returning Query Results in a Specified Order

You want to display the names, jobs, and salaries of employees in department 10 in order based on their salary (from lowest to highest).

```sql
select ename,job,sal
from emp
where deptno = 10
order by sal asc
```

### 2. Sorting by Multiple Fields

```sql
select empno, deptno, sal, ename, job
from emp
order by deptno, sal desc
```

### 3. Sorting by Substrings

You want to sort the results of a query by specific parts of a string. For example, you want to return employee names and jobs from table EMP and sort by **the last two characters in the JOB field**. The result set should look like the following:

```sql
select ename,job
from emp
order by substr(job,length(job)-2)
```

### 4. Sorting Mixed Alphanumeric Data

Solution not provided for MySQL

### 5. Dealing with Nulls When Sorting

```sql
/* NON-NULL COMM SORTED ASCENDING, ALL NULLS LAST */
select ename, sal, comm
from (
    select ename, sal, comm,
    case when comm is null then 0 else 1 end as is_null
    from emp
    ) x
order by is_null desc, comm
```

### 6. Sorting on a Data-Dependent Key

```sql
select ename, sal, job, comm
from emp
order by case when job = 'SALESMAN' then comm else sal end
```