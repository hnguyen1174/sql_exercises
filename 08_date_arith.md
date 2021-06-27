### Functions:

|Type|MySQL|SQL Server|
|:--:|:--:|:--:|
|Add two dates|`date_add()`|`dateadd()`|
|Subtract two dates|`datediff()`|`datediff()`|


### 1. Adding and Subtracting Days, Months, and Years

`MySQL`

```sql
select hiredate - interval 5 day as hd_minus_5D,
       hiredate + interval 5 day   as hd_plus_5D,
       hiredate - interval 5 month as hd_minus_5M,
       hiredate + interval 5 month as hd_plus_5M,
       hiredate - interval 5 year  as hd_minus_5Y,
       hiredate + interval 5 year  as hd_plus_5Y
from emp
where deptno=10
```

or 

```sql
select date_add(hiredate, interval -5 day)   as hd_minus_5D,
       date_add(hiredate, interval  5 day)   as hd_plus_5D,
       date_add(hiredate, interval -5 month) as hd_minus_5M,
       date_add(hiredate,interval  5 month) as hd_plus_5M,
       date_add(hiredate,interval -5 year)  as hd_minus_5Y,
       date_add(hiredate,interval  5 year)  as hd_plus_5DY
from emp
where deptno=10
```

`SQL Server`

```sql
select dateadd(day, -5, hiredate)   as hd_minus_5D,
       dateadd(day, 5, hiredate)    as hd_plus_5D,
       dateadd(month, -5, hiredate) as hd_minus_5M,
       dateadd(month, 5, hiredate)  as hd_plus_5M,
       dateadd(year, -5, hiredate)  as hd_minus_5Y,
       dateadd(year, 5, hiredate)   as hd_plus_5Y
from emp
where deptno = 10
```

### 2. Determining the Number of Days Between Two Dates

```sql
select datediff(day,allen_hd,ward_hd)
from (
    select hiredate as ward_hd
    from emp
    where ename = 'WARD'
) x,
(
    select hiredate as allen_hd from emp
    where ename = 'ALLEN'
) y
```