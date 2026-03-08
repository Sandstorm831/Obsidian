- `Exclusion constraint` can be taken as a more flexible version of `Unique constraint`. An `Exclude` constraint operator primarily ensure that no 2 rows can satisfy the specific given comparison completely.
	```sql

  >>> CREATE TABLE exclusion_example(
  >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>>   room_id INTEGER,
  >>>   reservation_period TSRANGE,
  >>>   EXCLUDE USING GIST (reservation_period WITH &&)
  >>> );
  CREATE TABLE

	```
	This constraint basically uses `GIST` (`Generalized Search Tree`, a special kind of index) to check the operator `(&&)` which is an `overlap` operator. 
	This operator checks if there is any overlap in `reservation_period` of any rows, if it's true then it rejects the insertion
	```sql
  >>>  INSERT INTO
  >>>   exclusion_example (room_id, reservation_period)
  >>> VALUES
  >>>   (1, '[2025-09-01 14:00, 2025-09-03 12:00]');
  INSERT 0 1

  >>> INSERT INTO
  >>>   exclusion_example (room_id, reservation_period)
  >>> VALUES
  >>>   (1, '[2025-09-02 14:00, 2025-09-04 12:00]');
  ERROR:  conflicting key value violates exclusion constraint "exclusion_example_reservation_period_excl"
  DETAIL:  Key (reservation_period)=(["2025-09-02 14:00:00","2025-09-04 12:00:00"]) conflicts with existing key (reservation_period)=(["2025-09-01 14:00:00","2025-09-03 12:00:00"]).
	```
	It works, but now it also rejects reservation in `2` different rooms with overlap which we don't want

- For room, we'll add another exclusion constraint
