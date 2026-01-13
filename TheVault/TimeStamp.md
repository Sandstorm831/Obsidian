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

- When lesser number of fractional seconds are taken, it gets rounded-off and can point to a future time
	```sql
  >>> SELECT now()::TIMESTAMPTZ(1), now()::TIMESTAMPTZ(0), now()::TIMESTAMPTZ;
              now             |            now            |               now
  -----------------------------+---------------------------+----------------------------------
  2025-12-25 09:46:19.9+05:30 | 2025-12-25 09:46:20+05:30 | 2025-12-25 09:46:19.900897+05:30
  (1 row)
	```
	here the entry with `0` fractional seconds point to `20th` second, whereas other entries points at `19th` second

- If you don't want to round-off, then you can simply truncate the output to seconds
	```sql
  >>> SELECT now()::TIMESTAMPTZ(1), now()::TIMESTAMPTZ(0), now()::TIMESTAMPTZ, date_trunc('second', now());
              now             |            now            |               now                |        date_trunc
  -----------------------------+---------------------------+----------------------------------+---------------------------
  2025-12-26 09:05:06.9+05:30 | 2025-12-26 09:05:07+05:30 | 2025-12-26 09:05:06.857043+05:30 | 2025-12-26 09:05:06+05:30
  (1 row)

	```
	Here you can see that, `date_trun` function truncated output to the second but second remained the same

- `Postgres` can communicate date-time in a number of formats, but it's recommended to stick with `ISO-8601` format
	```sql
  2025-01-31T11:40:08.23+05:30
  2025-01-31T11:40:08.23-05:30
  2025-01-31T11:40:08.23Z        ->  2025-01-31T11:40:08.23+00:00
	```
	`ISO 8601` standard, unambiguous and works best

- You can check the format of timestamp from the variable `DataStyle`
	```sql
  >>> SHOW DateStyle;
  DateStyle
  -----------
  ISO, DMY
  (1 row)
	```
	Here `ISO` represent the `ISO-8601` format, and `DMY` represent the format for parsing ambiguous `date-time` notation

- Ambiguous date-time formats can be interpreted differently in different parts of the world. `01/03/2025` : It can be interpreted as March 1st or January 3rd depending on where you are
- `DMY` format parses the ambiguous date-time notation as `Date/Month/Year`
	```sql
  >>> SHOW DateStyle;
  DateStyle
  -----------
  ISO, DMY
  (1 row)

  >>> SELECT '01/03/2025'::DATE;
      date
  ------------
  2025-03-01
  (1 row)

  >>> SET DateStyle='ISO, MDY';
  SET

  >>> SELECT '01/03/2025'::DATE;
      date
  ------------
  2025-01-03
  (1 row)
	```
	Here you can see when we change the format to `MDY` from `DMY`, the data and month got reversed.

- Unix timestamps are just number of seconds elapsed since `january 1, 1970 (midnight UTC/GMT)`. You can convert `unix` timestamp into `TIMESTAMPTZ` with the following function
	```sql
  >>> SELECT TO_TIMESTAMP(1694793600);
        to_timestamp
  ---------------------------
  2023-09-15 21:30:00+05:30
  (1 row)
  -- here we see an offset
	```
	here we see an offset of `+05:30`, which is because of `Postgres` setting. `Unix` timestamps are offset from UTC, so there timezone is UTC only.
	```sql
  >>> SELECT TO_TIMESTAMP(1694793600.232);
          to_timestamp
  -------------------------------
  2023-09-15 21:30:00.232+05:30
  (1 row)
	```
	`Unix` timestamps also supports fractional seconds which appear after decimal places
	