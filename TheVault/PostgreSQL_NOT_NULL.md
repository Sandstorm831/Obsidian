- `nulls` are by default **unknown values**, not `false`, not `0`, not `empty string`, not `empty` they are just **unknown**. On comparing any known value with NULL (unknown value), `postgres` will respond with `NULL` (which means, `postgres` doesn't know).
	```sql
  >>> SELECT NULL;
  ?column?
  ----------

  (1 row)

  >>> SELECT NULL = 1;
  ?column?
  ----------

  (1 row)
	```

- Most of the times you want your columns to be `NOT NULL`, leave your columns to `nullable` if you are sure that they have to be `nullable`
	```sql
  >>> CREATE TABLE null_example(
  >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>>   name TEXT NOT NULL,
  >>>   price NUMERIC NOT NULL CHECK(price > 0)
  >>> );
  CREATE TABLE

  >>> INSERT INTO null_example (name, price) VALUES ('hello', 233.1245);
  INSERT 0 1

  >>> SELECT * FROM null_example;
  id | name  |  price
  ----+-------+----------
    1 | hello | 233.1245
  (1 row)

	```
