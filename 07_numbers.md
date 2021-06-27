### Functions:

* `cume_dist()`
* `avg()`
* `min()` and `max()`
* `sum()` and `sum(distinct )`
* `count()`
* `exp()`: exponential
* `ln()`: natural log
* `dense_rank()`
* `coalesce()`

### 1. Computing an Average

```sql
select deptno, avg(sal) as avg_sal
from emp
group by deptno
```

### 2. Finding the Min/Max Value in a Column

```sql
select deptno, min(sal) as min_sal, max(sal) as max_sal
from emp
group by deptno
```

### 3. Summing the Values in a Column

```sql
select deptno, sum(sal) as total_for_dept
from emp
group by deptno
```

### 4. Counting Rows in a Table

```sql
select deptno, count(*)
from emp
group by deptno
```

### 5. Counting Values in a Column

Count the number of non-NULL values in the EMP table’s COMM column:

```sql
select count(comm) from emp
```

### 6. Generating a Running Total

```sql
select ename, sal,
sum(sal) over (order by sal, empno) as running_total
from emp
order by 2
```

### 7. Generating a Running Product

```sql
select empno, ename, sal,
exp(sum(ln(sal)) over (order by sal, empno)) as running_prod
from emp
where deptno = 10
```

### 8. Smoothing a Series of Values

(Not applicable)

### 9. Calculating a Mode

```sql
select sal from (
    select sal,
    dense_rank() over (order by cnt desc) as rnk
    from (
        select sal, count(*) as cnt
        from emp
        where deptno = 20
        group by sal
        ) x
    ) y
where rnk = 1
```

### 10. Calculating a Median

SQL Server

```sql
select percentile_cont(0.5)
within group(order by sal)
over()
from emp
where deptno=20
```

MySQL

MySQL doesn’t have a PERCENTILE_CONT function, which makes things trickier. To find the median, the values for SAL must be ordered from lowest to highest. The CUME_DIST function achieves this goal and labels each row with its percentile.

```sql
with rank_tab (sal, rank_sal) as
(
    select sal, cume_dist() over (order by sal)
    from emp
    where deptno=20
),

inter as (
    select sal, rank_sal from rank_tab
    where rank_sal>=0.5
union
    select sal, rank_sal from rank_tab 
    where rank_sal<=0.5
)

select avg(sal) as MedianSal from inter;
```

### 11. Determining the Percentage of a Total

MySQL

```sql
select (
    sum(case when deptno = 10 then sal end)/sum(sal)
      )*100 as pct
from emp
```

SQL Server

```sql
select distinct (d10/total)*100 as pct
from (
    select deptno,
    sum(sal) over() total,
    sum(sal) over (partition by deptno) d10
    from emp
) x
where deptno=10
```

### 12. Aggregating Nullable Columns

```sql
select avg(coalesce(comm,0)) as avg_comm
from emp
where deptno=30
```

### 13. Computing Averages Without High and Low Values

You want to compute an average, but you want to exclude the highest and lowest val‐ ues to (hopefully) reduce the effect of skew. In statistical language, this is known as a trimmed mean. 

MySQL

```sql
select avg(sal), from emp
where sal not in (
    (select min(sal) from emp),
    (select max(sal) from emp)
)
```

SQL Server

```sql
select avg(sal)
from (
    select sal, 
    min(sal) over() min_sal, 
    max(sal) over() max_sal
    from emp
    ) x
where sal not in (min_sal,max_sal)
```

### 14. Converting Alphanumeric Strings into Numbers

SQL Server

```sql
select cast(
    replace(
        translate('paul123f321', 'abcdefghijklmnopqrstuvwxyz', rpad('#',26,'#')),
        '#','')
        as integer ) as num
from t1
```

### 15. Changing Values in a Running Total

```sql
select case when trx = 'PY' 
       then 'PAYMENT'
       else 'PURCHASE'
       end trx_type,
       amt,
       sum(
           case when trx = 'PY' 
           then -atm else atm
           end
       ) over (order by id, amt) as balance
from V
```

### 16. Finding Outliers Using the Median Absolute Deviation

(Not applicable)

### 17. Finding Anomalies Using Benford’s Law

(Not applicable)