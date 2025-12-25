- `100::int4` is postgres equivalent of `cast(100 as int4)` in `sql`
- `pg_typeof(100::int4)` → it gives the data-type of the input
- decorated literals is similar to cast but not exactly same, so, cast should be used as default and not decorated literal
	```sql
	>>>> SELECT pg_typeof(INTEGER '100'); -- here INTEGER is the decorator and '100' is the literal
   pg_typeof
  -----------
   integer
  (1 row)
  
  >>>> SELECT pg_typeof(INTEGER 100);
  ERROR:  syntax error at or near "100"
  LINE 1: SELECT pg_typeof(INTEGER 100);

	```

- pg_column_size helps you to determine how much bytes used to store a particular value
	```sql
>>>> SELECT
>>>> pg_column_size(100::int2),
>>>> pg_column_size(100::numeric),
>>>> pg_column_size(1000000000.913848378478231::numeric),
>>>> pg_column_size(100000000034224829428923.29849138027480183202193::numeric);
 pg_column_size | pg_column_size | pg_column_size | pg_column_size
----------------+----------------+----------------+----------------
              2 |              8 |             20 |             30
(1 row)
	```