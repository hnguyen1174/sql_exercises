### Functions:

* `length()`: length of a string
* `replace(string, to_replace, replacement)`
* `translate()`
* `lower()`
* `cast(<> as integer)`
* `replicate(<str>, time)`
* `group_concat()` for MySQL and `string_agg()` for SQL server

### 1. Walking a String

You want to traverse a string to return each character as a row, but SQL lacks a loop operation. For example, you want to display the ENAME “KING” from table EMP as four rows, where each row contains just characters from KING.

This example uses table T10, which contains 10 rows (it has one column, ID, holding the values 1 through 10). The maximum number of rows that can be returned from this query is 10.

```sql
select substr(e.ename, iter.pos, 1) as C
from (select ename from emp where ename = 'KING') e,
(select id as pos from t10) iter
where iter.pos <= length(e.ename)
```

### 2. Embedding Quotes Within String Literals

The following three SELECTs highlight different ways you can create quotes: in the middle of a string and by themselves:

```sql
select 'g''day mate' qmarks from t1 union all
select 'beavers'' teeth'    from t1 union all
select ''''                 from t1
```

### 3. Counting the Occurrences of a Character in a String

You want to count the number of times a character or substring occurs within a given string. Consider the following string:
"10,CLARK,MANAGER"
You want to determine how many commas are in the string.

By subtracting the two lengths, you obtain the difference in terms of characters, which is the number of commas in the string. The last operation divides the differ‐ ence by the length of your search string. This division is necessary if the string you are looking for has a length greater than 1.

```sql
select (length('10,CLARK,MANAGER')-
       length(replace('10,CLARK,MANAGER',',','')))/length(',')
as cnt from t1
```

### 4. Removing Unwanted Characters from a String

You want to remove all zeros and vowels as shown by the following values in columns STRIPPED1 and STRIPPED2:

```sql
select ename,
replace(
    replace(
        replace(
            replace(
                replace(ename,'A',''),
            'E',''),
        'I',''),
    'O',''),
'U','') as stripped1,
sal,
replace(sal,0,'') stripped2
from emp
```

### 5. Separating Numeric and Character Data

SQL Server

```sql
select replace(translate(data,'0123456789','0000000000'),'0','') as ename,
cast(
    replace(translate(lower(data), 'abcdefghijklmnopqrstuvwxyz', replicate('z',26)), 'z','')
    as integer) as sal
from (select concat(ename, sal) as data from emp) x
```

### 6. Determining Whether a String Is Alphanumeric

MySQL

The character ^ is for negation, so the expression can be stated as “not numbers or letters.” A return value of 1 is true and 0 is false, so the whole expression can be stated as “return rows where anything other than numbers and letters is false.”

```sql
create view V as
    select ename as data
      from emp
     where deptno=10
     union all
    select concat(ename,', $',sal,'.00') as data
      from emp
     where deptno=20
     union all
    select concat(ename,deptno) as data
      from emp
     where deptno=30;

select data
from V
where data regexp '[^0-9a-zA-Z]' = 0;
```

SQL Server

Only 9 of the 14 rows from view V are alphanumeric. To find the rows that are alpha‐ numeric only, simply use the function TRANSLATE. In this example, TRANSLATE converts characters 0–9 and a–z to “a”. Once the conversion is done, the converted row is then compared with a string of all “a” with the same length (as the row). If the length is the same, then you know all the characters are alphanumeric and nothing else.

```sql
select data
from V
where translate(lower(data), '0123456789abcdefghijklmnopqrstuvwxyz', replicate('a',36)) = 
replicate('a',len(data))
```

### 7. Extracting Initials from a Name

Stewie Griffin > S.G.

SQL Server

```sql
select replace(
    replace(
        translate(replace('Stewie Griffin', '.', ''), 'abcdefghijklmnopqrstuvwxyz', replicate('#',26)),
    '#','')
' ','.') + '.'
from t1
```

### 8. Ordering by Parts of a String

MySQL

```sql
select ename
from emp
order by substr(ename,length(ename)-1,)
```

SQL Server

```sql
select ename
from emp
order by substring(ename,len(ename)-1,2)
```

### 9. Ordering by a Number in a String

SQL Server

```sql
select data
from V
order by
cast(
    replace(
        translate(
            data, repeat('#',length(data)),
            replace(translate(data,'##########','0123456789'), '#','')),
    '#','') 
as integer)

```

### 10. Creating a Delimited List from Table Rows

MySQL

```sql
select deptno,
group_concat(ename order by empno separator, ',') as emps
from emp
group by deptno
```

```sql
select deptno,
string_agg(ename order by empno separator, ',') as emps
from emp
group by deptno
```

### 11. Converting Delimited Data into a Multivalued IN-List

(Not applicable)

### 12. Alphabetizing a String

