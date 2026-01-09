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