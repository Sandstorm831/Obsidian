- It is a finite fixed list from which you can choose from, choosing `ENUM` will be bad option when the options are constantly changing
- It is stored as integer under the hood in the database, but depicted as string while reading
	```sql
  >>> CREATE TYPE mood AS ENUM ('happy', 'sad', 'neutral');
  CREATE TYPE
  >>>
  >>> CREATE TABLE enum_example (
  >>> current_mood mood
  >>> );
  CREATE TABLE

  >>> INSERT INTO enum_example (current_mood) VALUES ('happy'), ('sad');
  INSERT 0 2
  >>> INSERT INTO enum_example (current_mood) VALUES ('happy'), ('sad'), ('neutral');
  INSERT 0 3
  >>> INSERT INTO enum_example (current_mood) VALUES ('yolo');
  ERROR:  invalid input value for enum mood: "yolo"
  LINE 1: INSERT INTO enum_example (current_mood) VALUES ('yolo');
  >>> -- Here you can see that it is acting as a check constraint and is not letting unallowed values to be inserted

  >>> SELECT * FROM enum_example;
  current_mood
  --------------
  happy
  sad
  happy
  sad
  neutral
  (5 rows)

	```

- If we view the values in sorted order, the sort occurs according to the order in which values are inserted in `ENUM` data-type and not on the basis of the text of the values
	```sql
  >>> SELECT * FROM enum_example ORDER BY current_mood ;
  current_mood
  --------------
  happy
  happy
  sad
  sad
  neutral
  (5 rows)
  >>> -- 
  >>> -- it can be usefull when you want to sort in a particular order like ["extra-small", "small", "medium", "large", "extra-large"]
	```
since we inserted the values `happy`, `sad`, `neutral` in this order in the `ENUM`, so the sort follows the same. it can be useful when you want to sort in a particular order like `["extra-small", "small", "medium", "large", "extra-large"]`

- We can add value anywhere in the ENUM
	```sql
  >>> ALTER TYPE mood ADD VALUE 'excited';
  ALTER TYPE
  >>> INSER INTO enum_ex

  >>> INSERT INTO enum_example (current_mood) VALUES ('excited');
  INSERT 0 1
  >>> INSERT INTO enum_example (current_mood) VALUES ('excited');
  INSERT 0 1
  >>> SELECT * FROM enum_example ORDER BY current_mood ;
  current_mood
  --------------
  happy
  happy
  sad
  sad
  neutral
  excited
  excited
  (7 rows)
	```
	inserting value before a value
	```sql
  >>> ALTER TYPE mood ADD VALUE 'afraid' BEFORE 'sad';
  ALTER TYPE
  >>> INSERT INTO enum_example (current_mood) VALUES ('afraid');
  INSERT 0 1
  >>> INSERT INTO enum_example (current_mood) VALUES ('afraid');
  INSERT 0 1
  >>> SELECT * FROM enum_example ORDER BY current_mood ;
  current_mood
  --------------
  happy
  happy
  afraid
  afraid
  sad
  sad
  neutral
  excited
  excited
  (9 rows)
	```
	inserting a value after a value
	```sql
  >>> ALTER TYPE mood ADD VALUE 'melancholic' AFTER 'afraid';
  ALTER TYPE
  >>> INSERT INTO enum_example (current_mood) VALUES ('melancholic');
  INSERT 0 1
  >>> INSERT INTO enum_example (current_mood) VALUES ('melancholic');
  INSERT 0 1
  >>> SELECT * FROM enum_example ORDER BY current_mood ;
  current_mood
  --------------
  happy
  happy
  afraid
  afraid
  melancholic
  melancholic
  sad
  sad
  neutral
  excited
  excited
  (11 rows)
	```

- removing a value from the `ENUM` is not possible, so you have to `drop` it and `create` a new one
	```sql
  >>> CREATE TYPE mood_new AS ENUM ('happy', 'sad', 'neutral', 'afraid');
  CREATE TYPE
  >>> ALTER TABLE enum_example
  >>>     ALTER COLUMN current_mood TYPE mood_new
  >>>     USING current_mood::TEXT::mood_new;
  ERROR:  invalid input value for enum mood_new: "excited"
	```
	We are getting this error as there are some values which are not consistent with the new `ENUM` type, like 'excited'
	To solve this, we will start a transaction and all the changes in a transaction occur at once or all are `rolledback`
	```sql
  >>> BEGIN;  -- Begins the transaction
  BEGIN
  >>> UPDATE enum_example SET current_mood = 'neutral' WHERE current_mood NOT IN ('happy', 'sad', 'neutral', 'afraid'); -- It Updates all the values, which are not in the values given at the end, to 'neutral'
  UPDATE 4

  >>> ALTER TABLE enum_example ALTER COLUMN current_mood TYPE mood_new USING current_mood::TEXT::mood_new;  -- It alters the table, change the type of column 'current_mood' from 'mood' to 'mood_new'
																													-- all the values are first typecasted to text which are then typecasted to mood_new ENUM
  ALTER TABLE
  >>> COMMIT; Commits the Transaction
  COMMIT

  >>> SELECT * FROM enum_example ORDER BY current_mood ;
  current_mood
  --------------
  happy
  happy
  sad
  sad
  neutral
  neutral
  neutral
  neutral
  neutral
  afraid
  afraid
  (11 rows)

	```

- Now we have to 2 `enum` hanging out, let's see some inner workings of these `enums`
	```sql
  >>> SELECT * FROM pg_catalog.pg_enum;
    oid  | enumtypid | enumsortorder |  enumlabel
  -------+-----------+---------------+-------------
  16512 |     16510 |             1 | happy
  16514 |     16510 |             2 | sad
  16516 |     16510 |             3 | neutral
  16520 |     16510 |             4 | excited
  16521 |     16510 |           1.5 | afraid
  16523 |     16510 |          1.75 | melancholic
  16526 |     16525 |             1 | happy
  16528 |     16525 |             2 | sad
  16530 |     16525 |             3 | neutral
  16532 |     16525 |             4 | afraid
  (10 rows)
	```
	here you can see that bottom 4 have a different `enumtypid (16525)` from the the remaining on the top `(16510)`, Also, the `enums` are sorted on the order represented by the integers in the column `enumsortorder`
	```sql
  >>> SELECT * FROM pg_catalog.pg_enum WHERE enumtypid='16510' ORDER BY enumsortorder;
    oid  | enumtypid | enumsortorder |  enumlabel
  -------+-----------+---------------+-------------
  16512 |     16510 |             1 | happy
  16521 |     16510 |           1.5 | afraid
  16523 |     16510 |          1.75 | melancholic
  16514 |     16510 |             2 | sad
  16516 |     16510 |             3 | neutral
  16520 |     16510 |             4 | excited
	```
	the `.5` and `.75` are here because we inserted these values in the middle