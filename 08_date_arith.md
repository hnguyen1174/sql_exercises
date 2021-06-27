### Functions:

|Type|MySQL|SQL Server|
|:--:|:--:|:--:|
|Add two dates|`date_add()`|`dateadd()`|
|Subtract two dates|`datediff()`|`datediff()`|
|Format date ('Mon', 'Tue' etc.)|`date_format()`|`datename()`|
|Get year|`year()`|NA|
|Get month|`month()`|NA|
|Access the next row|`lead()`|`lead()`|



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

### 3. Determining the Number of Business Days Between Two Dates

Given two dates, you want to find how many “working” days are between them, including the two dates themselves.

MySQL

```sql
select sum(case when date_format(
    date_add(jones_hd, interval t500.id-1 DAY),'%a'
    ) in ('Sat','Sun')
    then 0 else 1 end) as days
    from (
        select max(case when ename = 'BLAKE' then hiredate end) as blake_hd,
               max(case when ename = 'JONES' then hiredate end) as jones_hd
               from emp where ename in ('BLAKE','JONES')
         ) x, t500
where t500.id <= datediff(blake_hd,jones_hd)+1
```

SQL Server

```sql
select sum(
    case when datename(dw, jones_hd+t500.id-1) in ('SATURDAY','SUNDAY')
    then 0 else 1 end) as days
    from (
        select max(case when ename = 'BLAKE' then hiredate end) as blake_hd,
               max(case when ename = 'JONES' then hiredate end) as jones_hd
               from emp where ename in ('BLAKE','JONES')
         ) x, t500
where t500.id <= datediff(day,jones_hd-blake_hd)+1
```

### 4. Determining the Number of Months or Years Between Two Dates

MySQL: Use the functions YEAR and MONTH to return the four-digit year and the two-digit month for the dates supplied:

```sql
select mnth, mnth/12 from (
    select (year(max_hd) - year(min_hd))*12 +
           (month(max_hd) - month(min_hd)) as mnth
    from (select min(hiredate) as min_hd, max(hiredate) as max_hd from emp) x
) y
```

SQL Server

```sql
select datediff(month,min_hd,max_hd),
       datediff(year,min_hd,max_hd)
       from (
           select min(hiredate) min_hd, max(hiredate) max_hd
from emp
) x
```

### 5. Determining the Number of Seconds, Minutes, or Hours Between Two Dates

MySQL
Use the DATEDIFF function to return the number of days between ALLEN_HD and WARD_HD. Then multiply to find each unit of time:

```sql
select datediff(day,allen_hd,ward_hd)*24 hr,
       datediff(day,allen_hd,ward_hd)*24*60 min,
       datediff(day,allen_hd,ward_hd)*24*60*60 sec
from (
    select max(case when ename = 'WARD' then hiredate end) as ward_hd,
    max(case when ename = 'ALLEN' then hiredate end) as allen_hd
    from emp) x
```

SQL Server

```sql
select datediff(day,allen_hd,ward_hd,hour) as hr,
       datediff(day,allen_hd,ward_hd,minute) as min, 
       datediff(day,allen_hd,ward_hd,second) as sec
from (
    select max(case when ename = 'WARD' then hiredate end) as ward_hd,
    max(case when ename = 'ALLEN' then hiredate end) as allen_hd
    from emp) x
```

### 6. Counting the Occurrences of Weekdays in a Year

(Not applicable)

### 7. Determining the Date Difference Between the Current Record and the Next Record

Use the lead function to access the next row. The SQL Server version of DATEDIFF is used here:

```sql
select x.ename, x.hiredate, x.next_hd,
       datediff(x.hiredate,x.next_hd,day) as diff
       from (
           select deptno, ename, hiredate,
           lead(hiredate) over (order by hiredate) as next_hd
       )
       from emp e) x
where e.deptno=10
```