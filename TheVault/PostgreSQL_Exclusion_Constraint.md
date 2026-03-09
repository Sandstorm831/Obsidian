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
	```sql

  >>> DROP TABLE exclusion_example ;
  DROP TABLE

  >>> CREATE EXTENSION IF NOT EXISTS btree_gist;
  CREATE EXTENSION

  -- GIST doesn't support strict equality comparison by default, this extension
  -- enables the GIST to incorporate strict equality comparisons from INTEGER and few other data types

  >>> CREATE TABLE exclusion_example(
  >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>>   room_id INTEGER,
  >>>   reservation_period TSRANGE,
  >>>   EXCLUDE USING GIST (room_id WITH =, reservation_period WITH &&)
  >>> );
  CREATE TABLE

  >>>  INSERT INTO
  >>>   exclusion_example (room_id, reservation_period)
  >>> VALUES
  >>>   (1, '[2025-09-01 14:00, 2025-09-03 12:00]');
  INSERT 0 1

  >>>  INSERT INTO
  >>>    exclusion_example (room_id, reservation_period)
  >>>  VALUES
  >>>    (2, '[2025-09-02 14:00, 2025-09-04 12:00]');
  INSERT 0 1

  -- now different rooms with overlapping TSRANGE are getting inserted without error
  -- but adding entries with same rooms and overlapping TSRANGE throws error

  >>>  INSERT INTO
  >>>    exclusion_example (room_id, reservation_period)
  >>>  VALUES
  >>>    (2, '[2025-09-02 14:00, 2025-09-04 12:00]');
  ERROR:  conflicting key value violates exclusion constraint "exclusion_example_room_id_reservation_period_excl"
  DETAIL:  Key (room_id, reservation_period)=(2, ["2025-09-02 14:00:00","2025-09-04 12:00:00"]) conflicts with
  existing key (room_id, reservation_period)=(2, ["2025-09-02 14:00:00","2025-09-04 12:00:00"]).

  >>> SELECT * FROM exclusion_example;
  id | room_id |              reservation_period
  ----+---------+-----------------------------------------------
    1 |       1 | ["2025-09-01 14:00:00","2025-09-03 12:00:00"]
    2 |       2 | ["2025-09-02 14:00:00","2025-09-04 12:00:00"]
  (2 rows)

	```

- We can turn the `exclusion constraint` to a `partial constraint` by adding a simple `WHERE` clause, as `postgres` ignores the `exclusion constraint` if the `WHERE` clause is NOT satisfied
	```sql
  >>> DROP TABLE exclusion_example ;
  DROP TABLE

  >>> CREATE TABLE exclusion_example(
  >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>>   room_id INTEGER,
  >>>   booking_status TEXT,
  >>>   reservation_period TSRANGE,
  >>>   EXCLUDE USING GIST (room_id WITH =, reservation_period WITH &&) WHERE (booking_status != 'canceled')
  >>> );
  CREATE TABLE

  -- where the booking_status is given 'canceled', Postgres will skip the Exclusion constraint entirely

  >>> INSERT INTO
  >>>   exclusion_example (room_id, booking_status, reservation_period)
  >>> VALUES
  >>>   (2,'confirmed', '[2025-09-02 14:00, 2025-09-04 12:00]');
  INSERT 0 1

  >>> INSERT INTO
  >>>   exclusion_example (room_id, booking_status, reservation_period)
  >>> VALUES
  >>>   (2,'canceled', '[2025-09-02 14:00, 2025-09-04 12:00]');
  INSERT 0 1

  >>> INSERT INTO
  >>> exclusion_example (room_id, booking_status, reservation_period)
  >>> VALUES
  >>> (2,'confirmed', '[2025-09-01 14:00, 2025-09-03 12:00]');
  ERROR:  conflicting key value violates exclusion constraint "exclusion_example_room_id_reservation_period_excl"
  DETAIL:  Key (room_id, reservation_period)=(2, ["2025-09-01 14:00:00","2025-09-03 12:00:00"]) conflicts with existing
  key (room_id, reservation_period)=(2, ["2025-09-02 14:00:00","2025-09-04 12:00:00"]).

  -- canceled booking is inserted without any issues, but confirmed booking caused the error
	```