- `NUMERIC` and `floating-point` types have `NaN` (*not a number*) but `INTEGER (INT2, INT4, INT8)` do not and will throw an error if tried to do so
- unbounded `NUMERIC` (`NUMERIC` without any `precision` and `scale`) have `INFINITY` (alias `inf`) but `INTEGER (INT2, INT4, INT8)` and bounded `NUMERIC` doesn't have `INFINITY` type. Same rules apply for `-ve` infinity, i.e., `-Infinity` (alias `-inf`)
- All `NaN's` are equal to all other `NaN's`
	```sql
  >>> select 'NaN'::numeric = 'NaN'::float8;
  ?column?
  ----------
    t
  (1 row)
	```

- All infinities are equal to all other infinities
	```sql
	>>> SELECT 'inf'::numeric = 'inf'::float8;
	 ?column?
	----------
	 t
	(1 row)
	```

- `NaN's` are greater than other numbers
	```sql
	>>>> SELECT 'NaN'::numeric > 10000000000000000000000000000000::numeric;
   ?column?
  ----------
   t
  (1 row)
	```

- `NaN` raise to the power 0 is 1
	```sql
	>>>> SELECT 'NaN' ^ 0;
	 ?column?
	----------
	        1
	(1 row)
	```