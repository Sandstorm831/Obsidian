- There's a subtle difference between `foreign key` and `foreign key constraint`. A `foreign key` is a just a column (or group of columns) that references a column (unique) in another table whereas a `foreign key constraint` is a database rule that enforces referential integrity between 2 tables.
	```sql
  >>> student_id INT,
  -- foreign key as it's a reference of id column in students table

  >>> student_id INT REFERENCES students(id),
  -- foreign key constraint as it enforces integrity between students table and current table
	```

- A `foreign key constraint` can only reference a column that have **unique values**. Syntax for creating foreign key constraint is as follows
	```sql

  >>> CREATE TABLE states (
  >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>>   name text
  >>> );
  CREATE TABLE

  >>> CREATE TABLE cities (
  >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>>   state_id BIGINT references states(id),
  >>>   name text
  >>> );
  CREATE TABLE

  >>> INSERT INTO states (name) VALUES ('haryana');
  INSERT 0 1
  >>> INSERT INTO states (name) VALUES ('rajasthan');
  INSERT 0 1
  >>> SELECT * FROM states;
  id |   name
  ----+-----------
    1 | haryana
    2 | rajasthan
  (2 rows)

  >>> INSERT INTO cities (state_id , name) VALUES (3, 'gurugram');
  ERROR:  insert or update on table "cities" violates foreign key constraint "cities_state_id_fkey"
  DETAIL:  Key (state_id)=(3) is not present in table "states".

  -- can't insert any entry that doesn't have a reference in states table

  >>> INSERT INTO cities (state_id , name) VALUES (1, 'gurugram');
  INSERT 0 1
  >>> INSERT INTO cities (state_id , name) VALUES (2, 'ajmer');
  INSERT 0 1
  >>> SELECT * FROM cities;
  id | state_id |   name
  ----+----------+----------
    2 |        1 | gurugram
    3 |        2 | ajmer
  (2 rows)
	```

- `Foreign key constraints` can be applied as a table constraint also
	```sql

  >>> DROP TABLE cities ;
  DROP TABLE
  >>> CREATE TABLE cities (
  >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>> name text,
  >>> state_id BIGINT,
  >>> foreign key (state_id) references states(id)
  >>> );
  CREATE TABLE

  >>> INSERT INTO cities (state_id , name) VALUES (2, 'ajmer');
  INSERT 0 1
  >>> INSERT INTO cities (state_id , name) VALUES (1, 'gurugram');
  INSERT 0 1

  >>> INSERT INTO cities (state_id , name) VALUES (3, 'gurugram');
  ERROR:  insert or update on table "cities" violates foreign key constraint "cities_state_id_fkey"
  DETAIL:  Key (state_id)=(3) is not present in table "states".

  >>> SELECT * FROM cities;
  id |   name   | state_id
  ----+----------+----------
    1 | ajmer    |        2
    2 | gurugram |        1
  (2 rows)

	```


- If you want a `foreign key` that's a combination of more than 1 column, you can use a `composite foreign key` and of course the number and type of constrained columns need to match number and type of referenced columns
	```sql
  >>> CREATE TABLE t1 (
  >>>   a integer PRIMARY KEY,
  >>>   b integer,
  >>>   c integer,
  >>>   FOREIGN KEY (b, c) REFERENCES other_table (c1, c2)
  >>> );
	```
	this composite constraint finds a `SINGLE ENTRY` in the `other_table` where `c1 = b` and `c2 = c`

- how it is different from separate foreign key constraints
	```sql
  >>> CREATE TABLE t1 (
  >>>   a integer PRIMARY KEY,
  >>>   b integer REFERENCES other_table(c1),
  >>>   c integer REFERNCES other_table(c2),
  >>> );
	```
	`composite constraint` is different from `separate foreign key constraint` such that:
	- separate constraint does not enforce that both values occur in the same row
	- composite constraint only needs the tuple `(c1, c2)` to be unique together whereas the separate constraint need both `c1` and `c2` to be unique in themselves

- When something in parent table (whose column are referenced as foreign keys) is updated or deleted there are a few options with which we can control what happens in child table (who referenced the parent table)
	
	1. **NO Action** : It is the default, and it prevents `updation/deletion` of the `referenced column` in the parent table if there are rows that are still being referenced anywhere.
		```sql
        >>> select * from states;
        id |    name
        ----+------------
        2 | rajasthan
        1 | haryana
        (2 rows)

        >>> DELETE from states where id = 1;
        ERROR:  update or delete on table "states" violates foreign key constraint "cities_state_id_fkey" on table "cities"
        DETAIL:  Key (id)=(1) is still referenced from table "cities".

        >>> SELECT * FROM cities;
        id |   name   | state_id
        ----+----------+----------
        1 | ajmer    |        2
        2 | gurugram |        1
        (2 rows)

		```
		
	2. **RESTRICT** : The result of the default (`No Action`) and `RESTRICT` are the same. The subtle difference is in their execution, `No Action` allows the check to be deferred to later in a transaction whereas `RESTRICT` doesn't allow the check to be deferred to later in a transaction.
		```sql
        >>> CREATE TABLE cities (
        >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
        >>>   name text,
        >>>   state_id BIGINT,
        >>>   foreign key (state_id) references states(id) on DELETE RESTRICT
        >>> );
        CREATE TABLE

        >>> INSERT INTO cities (name, state_id) VALUES ('ajmer', 2), ('gurugram', 1);
        INSERT 0 2
        >>> SELECT * FROM cities;
        id |   name   | state_id
        ----+----------+----------
        1 | ajmer    |        2
        2 | gurugram |        1
        (2 rows)

        >>> DELETE from states where id = 1;
        ERROR:  update or delete on table "states" violates foreign key constraint "cities_state_id_fkey" on table "cities"
        DETAIL:  Key (id)=(1) is still referenced from table "cities".
		```
	
	3. **CASCADE** : On deleting the parent row, it deletes all the rows in all the child tables where that row was being referenced
		```sql

        >>> DROP TABLE cities ;
        DROP TABLE

        >>> CREATE TABLE cities (
        >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
        >>> name text,
        >>> state_id BIGINT,
        >>> foreign key (state_id) references states(id) on DELETE CASCADE
        >>> );
        CREATE TABLE

        rgarg=# INSERT INTO cities (name, state_id) VALUES ('ajmer', 2), ('gurugram', 1);
        INSERT 0 2
        rgarg=# SELECT * FROM cities;
        id |   name   | state_id
        ----+----------+----------
        1 | ajmer    |        2
        2 | gurugram |        1
        (2 rows)

        rgarg=# DELETE from states where id = 1;
        DELETE 1
        rgarg=# SELECT * FROM cities;
        id | name  | state_id
        ----+-------+----------
        1 | ajmer |        2
        (1 row)

        -- as you can see, deleting the state where state_id = 1 from states table, 
        -- also led to deletion of the corresponding row in cities table
		```
	5. **SET NULL** : As name indicates, it sets the constrained column in the child tables to null if parent column is deleted
		```sql

        >>> DROP TABLE cities ;
        DROP TABLE

        >>> CREATE TABLE cities (
        >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
        >>> name text,
        >>> state_id BIGINT,
        >>> foreign key (state_id) references states(id) on DELETE SET NULL
        >>> );
        CREATE TABLE

        rgarg=# INSERT INTO states (name) values ('haryana');
        INSERT 0 1
        rgarg=# SELECT * FROM states;
        id |   name
        ----+-----------
        2 | rajasthan
        4 | haryana
        (2 rows)

        rgarg=# INSERT INTO cities (name, state_id) VALUES ('ajmer', 2), ('gurugram', 4);
        INSERT 0 2
        rgarg=# SELECT * FROM cities;
        id |   name   | state_id
        ----+----------+----------
        3 | ajmer    |        2
        4 | gurugram |        4
        (2 rows)

        rgarg=# DELETE from states where id = 2;
        DELETE 1
        rgarg=# SELECT * FROM cities;
        id |   name   | state_id
        ----+----------+----------
        4 | gurugram |        4
        3 | ajmer    |
        (2 rows)

        -- the state_id value for ajmer in the cities table is set to NULL 
        -- as we deleted the state with id = 2 from the states table
		```
		
	Cascade can be useful but it should be used with utmost care, as deleting one parent column can delete child columns, which in turn can be parent to other tables and this could lead to a whole lot of data to be deleted with a cascading effect. Use RESTRICT, SET NULL or SET DEFAULT preferably
