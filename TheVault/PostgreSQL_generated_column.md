- A `generated column` is a special column that is always computed from other columns
- Two types of `Generated columns`, `VIRTUAL` & `STORED`.
- `STORED` generated column is computed when it's written and it occupies a storage as a normal column, `VIRTUAL` generated column occupies no storage and is computed when it is read (virtual generated column is like a `VIEW`)
- `generated column` is by default `VIRTUAL`, you can specify `VIRTUAL` or `STORED` keyword to make it explicit (`VIRTUAL` columns are only available in `Postgres 18` or above)
	```sql
  >>> SELECT version();
                      version
  -----------------------------------------------------
  PostgreSQL 16.11 ...
  (1 row)

	```
	My `postgres` version is `16.11`, so it don't support `VIRTUAL generated columns`

	```sql
  >>> CREATE TABLE people(
  >>> height_cm numeric,
  >>> height_in numeric GENERATED ALWAYS AS (height_cm / 2.54) STORED
  >>> );
  CREATE TABLE

  >>> INSERT INTO people (height_cm) VALUES (180);
  INSERT 0 1
  >>> SELECT * FROM people ;
  height_cm |      height_in
  -----------+---------------------
        180 | 70.8661417322834646
  (1 row)
	```
	You can't insert `height_in` value explicitly as it is set to `GENERATED ALWAYS`, in the same way as we can't insert in IDENTITY Columns

	```sql
  >>> INSERT INTO people (height_cm, height_in) VALUES (180, 70.86);
  ERROR:  cannot insert a non-DEFAULT value into column "height_in"
  DETAIL:  Column "height_in" is a generated column.
	```

- Example
	```sql
  >>> CREATE TABLE users (
  >>>   email TEXT,
  >>>   email_domain TEXT GENERATED ALWAYS AS (split_part(email, '@', 2)) STORED
  >>> );
  CREATE TABLE

  >>> INSERT INTO users (email) VALUES ('rg@domain.com');
  INSERT 0 1

  >>> SELECT * FROM users ;
      email     | email_domain
  ---------------+--------------
  rg@domain.com | domain.com
	```


- There are a few restrictions that you may encounter while using GENERATED COLUMNS
	1. You have to reference current row (`data-entry`) and can't reference other rows (`data-entries`), tables or anything like that.
	2. Your function (expression) must be `deterministic` and not anything that produce different outputs given same inputs
	3. You can't reference another generated column
