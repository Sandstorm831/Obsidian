- ensures that data in a column or a set of columns is unique among all the rows
- Primary Key puts 2 constraints in place, not null and unique
	```sql

  >>> CREATE TABLE unique_example(
  >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>> product_number text unique,
  >>> name text not null,
  >>> price numeric not null check(price>0)
  >>> );
  CREATE TABLE

  >>> INSERT INTO unique_example (product_number, name, price) VALUES ('ABC', 'ABC123', 234.24);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_number, name, price) VALUES ('ABC', 'ABC123', 234.24);
  ERROR:  duplicate key value violates unique constraint "unique_example_product_number_key"
  DETAIL:  Key (product_number)=(ABC) already exists.

	```

- more than 1 `NULL` values can be added to the `UNIQUE contraint` column as it's treated as an unknown value and thus distinct
	```sql

  >>> INSERT INTO unique_example (product_number, name, price) VALUES (NULL, 'ABC123', 234.24);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_number, name, price) VALUES (NULL, 'ABC123', 234.24);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_number, name, price) VALUES (NULL, 'ABC123', 234.24);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_number, name, price) VALUES (NULL, 'ABC123', 234.24);
  INSERT 0 1
  >>> SELECT * FROM unique_example;
  id | product_number |  name  | price
  ----+----------------+--------+--------
    1 | ABC            | ABC123 | 234.24
    3 |                | ABC123 | 234.24
    4 |                | ABC123 | 234.24
    5 |                | ABC123 | 234.24
    6 |                | ABC123 | 234.24
  (5 rows)

	```

- If you don't want `NULLs` to be distinct you can do that too
	```sql

  >>> DROP TABLE unique_example ;
  DROP TABLE

  >>> CREATE TABLE unique_example (
  >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>> product_number text unique nulls not distinct,
  >>> name text not null,
  >>> price numeric not null check(price>0)
  >>> );
  CREATE TABLE

  >>> INSERT INTO unique_example (product_number, name, price) VALUES ('ABC', 'ABC123', 234.233);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_number, name, price) VALUES (NULL, 'ABC123', 234.233);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_number, name, price) VALUES (NULL, 'ABC123', 234.233);
  ERROR:  duplicate key value violates unique constraint "unique_example_product_number_key"
  DETAIL:  Key (product_number)=(null) already exists.

	```

- You can add table constraints with 2 or more column being unique together
	```sql

  >>> drop table unique_example ;
  DROP TABLE

  >>> CREATE TABLE unique_example(
  >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>> product_numer text not null,
  >>> brand text not null,
  >>> name text not null,
  >>> price numeric not null check(price > 0),
  >>> unique(product_numer, brand)
  >>> );
  CREATE TABLE

  >>> INSERT INTO unique_example (product_numer, brand, name, price) VALUES ('123','ABC', 'ABC123', 234.233);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_numer, brand, name, price) VALUES ('123','XYZ', 'ABC123', 234.233);
  INSERT 0 1
  >>> INSERT INTO unique_example (product_numer, brand, name, price) VALUES ('124','XYZ', 'ABC123', 234.233);
  INSERT 0 1
  >>> SELECT * FROM unique_example;
  id | product_numer | brand |  name  |  price
  ----+---------------+-------+--------+---------
    1 | 123           | ABC   | ABC123 | 234.233
    2 | 123           | XYZ   | ABC123 | 234.233
    3 | 124           | XYZ   | ABC123 | 234.233
  (3 rows)

  >>> INSERT INTO unique_example (product_numer, brand, name, price) VALUES ('124','XYZ', 'ABC123', 234.233);
  ERROR:  duplicate key value violates unique constraint "unique_example_product_numer_brand_key"
  DETAIL:  Key (product_numer, brand)=(124, XYZ) already exists.

	```

