- you can store `date` & `time` separately in different columns
- you can store `datetime` in 2 formats : `timestamp without timezone` & `timestamp with timezone`. For `timestamp without timezone`, `postgres` makes no effort to do any conversion, for `timestamp with timezone`, `Postgres` convert the timestamp in `UTC` and store it in the database and when you pull it back, `Postgres` convert the timestamp according to your `timezone`. `Timestamp with timezone` is the preferred one.
	```sql
  >>> CREATE TABLE timestamp_example(
  >>> timestamp TIMESTAMP
  >>> );
  CREATE TABLE
  >>> \d timestamp_example
                      Table "public.timestamp_example"
    Column   |            Type             | Collation | Nullable | Default
  -----------+-----------------------------+-----------+----------+---------
  timestamp | timestamp without time zone |           |          |
	```
	here, the default `timestamp type` is `TIMESTAMP without TIME ZONE`, `TIMESTAMP` can be considered it's alias
	```sql
  >>> CREATE TABLE timestamp_example(
  >>> timestamp TIMESTAMPTZ(3)
  >>> );
  CREATE TABLE
  >>> \d timestamp_example
                      Table "public.timestamp_example"
    Column   |            Type             | Collation | Nullable | Default
  -----------+-----------------------------+-----------+----------+---------
  timestamp | timestamp(3) with time zone |           |          |
	```
	here, `TIMESTAMPTZ` is the alias for timestamp type `TIMESTAMP with TIME ZONE`, the argument that it takes is the number of fractional seconds you want to store, it ranges from `[0 6]` both including
	```sql
  >>> SELECT now()::TIMESTAMPTZ(4);
                now
  --------------------------------
  2025-12-25 09:43:51.8182+05:30
  (1 row)
  -- upto 4 places of fractional seconds are taken

  >>> SELECT now()::TIMESTAMPTZ;
                now
  ----------------------------------
  2025-12-25 09:44:26.602192+05:30
  (1 row)
	```
	maximum places (6) of fractional seconds are taken

- 