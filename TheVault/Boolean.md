- `Postgres` has a separate data-type for boolean and doesn't use integer under the hood
- It can contain three states, which are `TRUE`, `FALSE` & `NULL` (`UNKNOWN`)
	```sql
  >>> CREATE TABLE boolean_example(
  >>> status BOOLEAN
  >>> );
  CREATE TABLE
  >>> INSERT INTO boolean_example (status) VALUES
  >>> (TRUE),   -- TRUE
  >>> (FALSE),  -- FALSE
  >>> ('t'),    -- TRUE
  >>> ('f'),    -- FALSE
  >>> ('true'), -- TRUE
  >>> ('false'),    -- FALSE
  >>> ('1'),        -- TRUE (must be quoted, can't be an integer)
  >>> ('0'),        -- FALSE (must be quoted, can't be an integer)
  >>> ('on'),       -- TRUE
  >>> ('off'),      -- FALSE
  >>> ('yes'),      -- TRUE
  >>> ('no'),       -- FALSE
  >>> (NULL);       -- NULL essentially means unknown
  INSERT 0 13
  >>> SELECT * FROM boolean_example;
  status
  --------
  t
  f
  t
  f
  t
  f
  t
  f
  t
  f
  t
  f
  NULL
  (13 rows)
	```

- We can explicitly cast an integer to a boolean, but we can't insert an explicit integer as a boolean
	```sql
  >>> SELECT 1::BOOLEAN;
  bool
  ------
  t
  (1 row)

  >>> SELECT 2::BOOLEAN;
  bool
  ------
  t
  (1 row)

  >>> SELECT 0::BOOLEAN;
  bool
  ------
  f
  (1 row)
  
  >>> INSERT INTO boolean_example (status) VALUES (1);
  ERROR:  column "status" is of type boolean but expression is of type integer
  LINE 1: INSERT INTO boolean_example (status) VALUES (1);

  HINT:  You will need to rewrite or cast the expression.
	```

- it's size is `1 byte` which is super compact
	```sql
  >>> SELECT pg_column_size(1::boolean), pg_column_size(1::int2);
  pg_column_size | pg_column_size
  ----------------+----------------
                1 |              2
  (1 row)
	```