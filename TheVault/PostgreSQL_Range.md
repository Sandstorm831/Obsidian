- `Range` types are more like a `meta-type`. It represents a range of value of an element type (which is called range's `sub-type`)
- `tsrange` is the range data-type which has timestamp as it's `subtype`, it can be used to represent the range of time for some reservation or anything else.
- It can be declared by a `lower-bound` and an `upper-bound`, either inclusive or exclusive, you can also have unbounded lower or unbounded upper.
- You can do queries like
  - if certain discrete point present in the range
  - do these ranges overlap
  - do one of the range contain other

	```sql
  >>> SELECT '[1,5]'::int4range;
  int4range
  -----------
  [1,6)
  (1 row)

  >>> SELECT '[1,5)'::int4range;
  int4range
  -----------
  [1,5)
  (1 row)

  >>> SELECT '[1,5]'::numrange;
  numrange
  ----------
  [1,5]
  (1 row)
	```
	- For `int4range`, `[1,5] and [1,6)` represent the same set of integers, so `int4range` simply transformed the way of writing the output
	- for `numrange`, `[1,5] and [1,6)` contain different set of `numerics`, as `[1,6)` contain an extra range of numbers in `(5,6)` apart from the original input `[1,5]`, thus the final output remained same

- you can define the `ranges` using construction function
	```sql
  >>> SELECT int4range(1,5);
  int4range
  -----------
  [1,5)
  (1 row)

  -- the default format is 1st number included and last number excluded
  -- there's a third parameter also which you can use to change this

  >>> SELECT int4range(1,5,'[]');
  int4range
  -----------
  [1,6)
  (1 row)
	```

- Ranges can be unbounded on either of the side or both depicting or they can be empty depicting no range at all
	```sql

  >>> CREATE TABLE range_example (
  >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>> int_range INT4RANGE,
  >>> num_range NUMRANGE,
  >>> date_range DATERANGE,
  >>> ts_range TSRANGE
  >>> );
  CREATE TABLE

  >>>  INSERT INTO range_example ( int_range, num_range, date_range, ts_range)
  >>>   VALUES
  >>> ('[1,11)', '[0.5,5.5)', '["2023-01-01", "2024-01-01")', '["2023-09-01 00:00:00", "2023-09-30 23:59:00"]'),
  >>> ('[2,101)', '(0.0,10.0]', '["2022-01-01", "2022-06-01")', '("2023-01-01 00:00:00", "2023-01-10 12:00:00"]'),
  >>> ('[10,20)', '[1.0,2.0)', 'empty', 'empty'), -- 'empty' -> Range exists but contains no elements
  >>> ('[5,)', '[1.0,)', '(,)', '(, "2023-01-01 00:00:00")'); -- '(,)' -> Range with no upper or lower bound at all
  INSERT 0 4

  >>> SELECT * FROM range_example;
  id | int_range | num_range  |       date_range        |                   ts_range
  ----+-----------+------------+-------------------------+-----------------------------------------------
    1 | [1,11)    | [0.5,5.5)  | [2023-01-01,2024-01-01) | ["2023-09-01 00:00:00","2023-09-30 23:59:00"]
    3 | [2,101)   | (0.0,10.0] | [2022-01-01,2022-06-01) | ("2023-01-01 00:00:00","2023-01-10 12:00:00"]
    5 | [10,20)   | [1.0,2.0)  | empty                   | empty
    6 | [5,)      | [1.0,)     | (,)                     | (,"2023-01-01 00:00:00")
  (4 rows)
	```
	unbounded ranges can be used in a case like, a particular set of rooms are going under repair so for those rooms we can add an unbounded booked date range indicateing it's occupied for forever, thus won't be picked for any future reservation

- you can use the `(@>)` operator to check if a range contains a value
	```sql

  >>> SELECT * FROM range_example WHERE int_range @> 5;
  id | int_range | num_range  |       date_range        |                   ts_range
  ----+-----------+------------+-------------------------+-----------------------------------------------
    1 | [1,11)    | [0.5,5.5)  | [2023-01-01,2024-01-01) | ["2023-09-01 00:00:00","2023-09-30 23:59:00"]
    3 | [2,101)   | (0.0,10.0] | [2022-01-01,2022-06-01) | ("2023-01-01 00:00:00","2023-01-10 12:00:00"]
    6 | [5,)      | [1.0,)     | (,)                     | (,"2023-01-01 00:00:00")
  (3 rows)

  -- Here, rows 1 & 2 contains 5 easily but for 3rd row, square bracket is used at starting which means inclusive
  -- thus, 5 is included in the range and so it's also picked. 
  -- If we change the square bracket to parenthesis it won't be included in the query

  >>> UPDATE range_example set int_range = '(5,)' WHERE id = 6;
  UPDATE 1
  >>> SELECT * FROM range_example WHERE int_range @> 5;
  id | int_range | num_range  |       date_range        |                   ts_range
  ----+-----------+------------+-------------------------+-----------------------------------------------
    1 | [1,11)    | [0.5,5.5)  | [2023-01-01,2024-01-01) | ["2023-09-01 00:00:00","2023-09-30 23:59:00"]
    3 | [2,101)   | (0.0,10.0] | [2022-01-01,2022-06-01) | ("2023-01-01 00:00:00","2023-01-10 12:00:00"]
  (2 rows)

	```

- To find a range that overlaps with another range, you can use `(&&)` operator
	```sql

  >>> SELECT * FROM range_example WHERE int_range && '[10,20)';
  id | int_range | num_range  |       date_range        |                   ts_range
  ----+-----------+------------+-------------------------+-----------------------------------------------
    1 | [1,11)    | [0.5,5.5)  | [2023-01-01,2024-01-01) | ["2023-09-01 00:00:00","2023-09-30 23:59:00"]
    3 | [2,101)   | (0.0,10.0] | [2022-01-01,2022-06-01) | ("2023-01-01 00:00:00","2023-01-10 12:00:00"]
    5 | [10,20)   | [1.0,2.0)  | empty                   | empty
    6 | [6,)      | [1.0,)     | (,)                     | (,"2023-01-01 00:00:00")
  (4 rows)

  -- PostgreSQL implicitly casts '[10,20)' to int4range 
  -- As int4range matched for the operator in place of int4range && ?
  
  -- rows 2,3 & 4 completely overlaps the range [10, 20) but 
  -- row 1 barely overlaps as 10 is the only integer included in both.
  -- If we change the query to exclude 10, then we won't get the first row

  >>> SELECT * FROM range_example WHERE int_range && '(10,20)';
  id | int_range | num_range  |       date_range        |                   ts_range
  ----+-----------+------------+-------------------------+-----------------------------------------------
    3 | [2,101)   | (0.0,10.0] | [2022-01-01,2022-06-01) | ("2023-01-01 00:00:00","2023-01-10 12:00:00"]
    5 | [10,20)   | [1.0,2.0)  | empty                   | empty
    6 | [6,)      | [1.0,)     | (,)                     | (,"2023-01-01 00:00:00")
  (3 rows)

	```


- To find intersections, you can use `(*)` operator
	```sql

  >>> SELECT int4range(10,20) * int4range(15,25);
  ?column?
  ----------
  [15,20)
  (1 row)

  >>> SELECT pg_typeof(int4range(10,20) * int4range(15,25));
  pg_typeof
  -----------
  int4range
  (1 row)

  -- here 20 is excluded as first range [10,20) have 20 excluded
	```

- To find the upper and lower bound `programmatically`, you have to use `(upper, lower)` & `(upper_inc, lower_inc)`
	```sql

  >>> SELECT upper(int4range(10,20,'[]')), upper_inc(int4range(10,20,'[]'));
  upper | upper_inc
  -------+-----------
      21 | f
  (1 row)

  -- here, upper bound of range [10,20] is given as 20 and not 21, 
  -- upper_inc returned false which means 21 is not inclusive and thus upper bound is 20
  -- such kind of arrangement is done as in case of numeric, 
  -- this is the only way you can write 21 exclusive, you can not write the numeric (not int) which comes just before 21.

  >>> SELECT upper(numrange(10,21)), upper_inc(int4range(10,21));
  upper | upper_inc
  -------+-----------
      21 | f
  (1 row)

	```


- Every range has a corresponding `multirange` type, it's an ordered list of `non-contiguous(not continuous)`, `non-empty`, `non-null` ranges.
	```sql

  >>> SELECT '{[3,7), [8,9)}'::int4multirange;
  int4multirange
  ----------------
  {[3,7),[8,9)}
  (1 row)
	```

- We can use contains operator `(@>)` on `multirange`
	```sql

  >>> SELECT '{[3,7), [8,9)}'::int4multirange @> 3;
  ?column?
  ----------
  t
  (1 row)

  >>> SELECT '{[3,7), [8,9)}'::int4multirange @> 4;
  ?column?
  ----------
  t
  (1 row)

  >>> SELECT '{[3,7), [8,9)}'::int4multirange @> 7;
  ?column?
  ----------
  f
  (1 row)

  >>> SELECT '{[3,7), [8,9)}'::int4multirange @> 8;
  ?column?
  ----------
  t
  (1 row)

  >>> SELECT '{[3,7), [8,9)}'::int4multirange @> 9;
  ?column?
  ----------
  f
  (1 row)

  -- first range is [3,7), which included integers from 3 to 6 excluding 7,
  -- thus 1st and 2nd query returned true result but 3rd query returned false
  -- Second range [8,9) only contains 8 and 9 is excluded,
  -- thus 4th query returned true but 5th query retured false

	```