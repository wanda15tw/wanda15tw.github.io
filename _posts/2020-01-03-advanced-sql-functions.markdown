---
layout: post
title:  "Advanced SQL Functions"
date:   2020-01-03 21:20:00 +0530
categories: DataScience SQL
---


###### tags: `cheatsheet`, `SQL`

In real life, as a data scientist, I spend a lot of time writing SQL queries to understand data and perform data cleansing, even some basic analytics. Because the huge amount of data are stored in database, before pulling these datasets to local or migrating to other clusters and using Python to analyze, we need to have some hang of the data to be efficient. SQL database is so much better at performing data cleaning and integration. 

Here is a few SQL functions that I learned over the course, but not immediately after started the SQL journey. I make this note for myself to get the correct syntax and usage as I tend to be forgetful lately, and also hope to share with people who would like to advance their SQL queries.

## subquery - WITH
```
WITH
   subquery_name
AS
  (the aggregation SQL statement)
SELECT
  (query naming subquery_name);
```
This `with` statement helps to make the subquery easier to read.
e.q.
```
WITH
   sum_sales AS
      ( select
      sum(qty) all_sales from sales ),
   number_stores AS
      ( select
      count(*) nbr_stores from stores ),
   sales_by_store AS
      ( select
      stor_name, sum(qty) store_sales from
      stores natural join sales
      group by stor_name)
SELECT
   stores.stor_name
FROM
   stores,
   sum_sales,
   number_stores,
   sales_by_store
where
   store_sales > (all_sales / nbr_stores);
```

Left join using `WITH` subquery
```
WITH a as
    (select * from table1),
    b as 
    (select * from table2)
SELECT 
    a.*, b.* from a left join b
ON a.key = b.key;
```
## ROWNUMBER | COUNT OVER (PARTITION BY ... [ORDER BY ...])
```
SELECT 
    department_id, 
    last_name, 
    employee_id, 
    ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY employee_id) AS emp_id
FROM employees;
```
```
SELECT empno,
       ename,
       deptno,
       sal,
       COUNT(*) OVER (PARTITION BY deptno) AS amount_by_dept
FROM   emp;
```
Aggregation `GROUP BY` has to be performed on all selected columns, but a lot of my cases are those columns are not numerical so cannot be aggregated easily, either meaningfully. These functions are extremely handy when doing data clean up and there are inconsistent but duplicated entries. 

Use `COUNT OVER (PARTITION BY)` to count number of duplicates per master column's value, and `ROWNUMBER OVER ...` for indexing and selecting rows.

## LISTAGG
```
select d.dname,   
       listagg ([DISTINCT] e.job,', ' on overflow truncate with count)     
                within group (order by e.job) jobs  
  from scott.dept d, scott.emp e     
 where d.deptno = e.deptno     
 group by d.dname
```
If I really have to remove duplicates to clean up and there are inconsistent categorical entries, I aggregate them into a list using `LISTAGG`, or specifically `LISTAGG DISTINCT` to make distinct lists.

## LAG, LEAD
```
LAG
  { ( value_expr [, offset [, default]]) [ { RESPECT | IGNORE } NULLS ] 
  | ( value_expr [ { RESPECT | IGNORE } NULLS ] [, offset [, default]] )
  }
  OVER ([ query_partition_clause ] order_by_clause)

LEAD
  { ( value_expr [, offset [, default]] ) [ { RESPECT | IGNORE } NULLS ] 
  | ( value_expr [ { RESPECT | IGNORE } NULLS ] [, offset [, default]] )
  }
  OVER ([ query_partition_clause ] order_by_clause)
```
The LAG function is used to access data from a previous row. The LEAD function is used to return data from rows further down the result set. Use `OVER (PARTITION BY... ORDER BY ...)` to define previous or further.

`offset` - The number of rows preceeding/following the current row, from which the data is to be retrieved. The default value is 1.
`default` - The value returned if the offset is outside the scope of the window. The default value is NULL.

e.g.
```
SELECT deptno,
       empno,
       ename,
       job,
       sal,
       LAG(sal, 1, 0) OVER (PARTITION BY deptno ORDER BY sal) AS sal_prev
FROM   emp;
```
## FIRST_VALUE, LAST_VALUE, NTH_VALUE
Similar to `LEAD` and `LAG`, but query first, last or nth values. *Note for the special `RANGE` in the syntax*.

* Syntax 
```
FIRST_VALUE | LAST_VALUE (expression)
 [RESPECT NULLS | IGNORE NULLS]
 OVER ([query_partition_clause] [order_by_clause [windowing_clause]])
```
* Example

```
SELECT DISTINCT LAST_VALUE(salary)
 OVER (ORDER BY salary ASC
       RANGE BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING)
       AS "HIGHEST"
FROM employees;
```


## SPOOL
Script to export query to .csv file.
```
SPOOL filename.csv
select /*csv*/ * from table;
SPOOL OFF
```


## Reference
* Internet search: Oracle SQL