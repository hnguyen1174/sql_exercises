### Functions:

* `sum(distinct <col name>)`

### Clauses:

* `union all`: stacking one rowset atop another
* `with <tbl name> as ()`: create temp table
* `select distinct`
* `left outer join`
* `full outer join`
* `union`: stacking one rowset atop another, removing duplicate rows

### 1. Stacking One Rowset atop Another

```sql
select ename as ename_and_dname, deptno
from emp 
where deptno = 10
union all
select dname, deptno
from dept
```

### 2. Combining Related Rows

The solution is an example of a join, or more accurately an equi-join, which is a type of inner join.

Join table EMP to table DEPT on DEPTNO:

```sql
select e.ename, d.loc
from emp e, dept d
where e.deptno = d.deptno
and e.deptno = 10
```

### 3. Finding Rows in Common Between Two Tables

You want to find common rows between two tables, but there are multiple columns on which you can join. 

```sql
with cte as (
select ename, job, sal
    from emp
    where job = 'CLERK'
)

select e.empno, e.ename, e.job, e.sal, e.deptno
from emp e, cte v
where e.ename = v.ename
and e.job = v.job
and e.sal = v.sal
```

### 4. Retrieving Values from One Table That Do Not Exist in Another

**Note:** In SQL, “TRUE or NULL” is TRUE, but “FALSE or NULL” is NULL! You must keep this in mind when using IN predicates, and when performing logical OR evaluations and NULL values are involved.

```sql
select distinct deptno
from dept
where deptno not in (select deptno from emp)
```

To avoid the problem with NOT IN and NULLs, use a correlated subquery in conjunction with NOT EXISTS. The term correlated subquery is used because rows from the outer query are referenced in the subquery. 

```sql
select d.deptno
from dept d
where not exists (
    select 1
    from emp e
    where d.deptno = e.deptno
)
```

1. The subquery is executed to see whether the department number exists in the EMP table. Note the condition D.DEPTNO = E.DEPTNO, which brings together the department numbers from the two tables.

2. If the subquery returns results, then EXISTS (...) evaluates to true and NOT EXISTS (...) thus evaluates to FALSE, and the row being considered by the outer query is discarded.

3. If the subquery returns no results, then NOT EXISTS (...) evaluates to TRUE, and the row being considered by the outer query is returned (because it is for a department not represented in the EMP table).

### 5. Retrieving Rows from One Table That Do Not Correspond to Rows in Another

This solution works by outer joining and then keeping only rows that have no match. This sort of operation is sometimes called an anti-join.

```sql
select d.*
from dept d left outer join emp e
on (d.deptno = e.deptno)
where e.deptno is null
```

### 6. Adding Joins to a Query Without Interfering with Other Joins

```sql
select e.ename, d.loc, eb.received
from emp e join dept d
  on (e.deptno=d.deptno)
left join emp_bonus eb
  on (e.empno=eb.empno)
order by 2
```

You can also use a **scalar subquery** (a subquery placed in the SELECT list) to mimic an outer join:

```sql
select e.ename, d.loc,
(select eb.received from emp_bonus eb
  where eb.empno=e.empno) as received
from emp e, dept d
where e.deptno=d.deptno
order by 2
```

### 7. Determining Whether Two Tables Have the Same Data

1. Find rows in table EMP that do not exist in view V.
2. Combine (UNION ALL) those rows with rows from view V that do not exist in table EMP.

If the tables in question are equal, then no rows are returned. If the tables are differnt, the rows causing the difference are returned. As an easy first step when comparing tables, you can compare the cardinalities alone rather than including them with the data comparison.

### 8. Identifying and Avoiding Cartesian Products

If a join key is not specified, Cartesian product will happen.

```sql
select e.ename, d.loc
from emp e, dept d
where e.deptno = 10
```

Correct:

```sql
select e.ename, d.loc
from emp e, dept d
where e.deptno = 10
and d.deptno = e.deptno
```

### 9. Performing Joins When Using Aggregates

```sql
select deptno, 
       sum(distinct sal) as total_sal,
       sum(bonus) as total_bonus
from (
    select e.empno, e.ename, e.sal, e.deptno,
    e.sal*case when eb.type = 1 then .1
               when eb.type = 2 then .2
               else .3
    end as bonus
    from emp e, emp_bonus eb
    where e.empno = eb.empno
    and e.deptno = 10
    ) x
group by deptno
```

### 10. Performing Outer Joins When Using Aggregates

Modify the employee bonus table as follows:

```sql
INSERT INTO emp_bonus VALUES ('7934','2005-03-17','1');
INSERT INTO emp_bonus VALUES ('7934','2005-02-15','2');
```

```sql
select deptno,
sum(distinct sal) as total_sal,
sum(bonus) as total_bonus
from (
    select e.empno, e.ename, e.sal, e.deptno,
    e.sal*case when eb.type is null then 0
               when eb.type = 1 then .1
               when eb.type = 2 then .2
    else .3 end as bonus
    from emp e left outer join emp_bonus eb
    on (e.empno = eb.empno)
    where e.deptno = 10
    ) x
group by deptno
```

Using windows function:

```sql
select distinct deptno,total_sal,total_bonus
from (
    select e.empno, e.ename,
    sum(distinct e.sal) over (partition by e.deptno) as total_sal,
    e.deptno,
    sum(e.sal*case when eb.type is null then 0
                   when eb.type = 1 then .1
                   when eb.type = 2 then .2
                   else .3 end) over
    (partition by deptno) as total_bonus
    from emp e left outer join emp_bonus eb
    on (e.empno = eb.empno)
    where e.deptno = 10) x
```

### 11. Returning Missing Data from Multiple Tables

Use the explicit FULL OUTER JOIN command to return missing rows from both tables along with matching rows:

```sql
select d.deptno, d.dname, e.ename
from dept d full outer join emp e
on (d.deptno=e.deptno)
```

union will remove duplicate rows:

```sql
select d.deptno,d.dname,e.ename
from dept d right outer join emp e
on (d.deptno=e.deptno)
union
select d.deptno,d.dname,e.ename
from dept d left outer join emp e
on (d.deptno=e.deptno)
```

### 12. Using NULLs in Operations and Comparisons

Use a function such as COALESCE to transform the NULL value into a real value that can be used in standard evaluation:

```sql
select ename, comm
from emp
where coalesce(comm,0) < (select comm
from emp where ename = 'WARD')
```


