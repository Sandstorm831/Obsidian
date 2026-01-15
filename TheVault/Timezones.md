> Recommendations :
> 1. Keep it in UTC for as long as possible, convert it whatever user needs at the latest moment possible
> 2. Use named TIME ZONE, not hours offsets or abbreviations


- `TIME ZONE` variable dictates the `session` timezone, thus all the queries will which include `timezones` will convert the internally stored `UTC` time to `TIME ZONE` specified timezone and then display that, it's `session` variable and it will be reset after you disconnect and reconnect to the `database/cluster TIME ZONE`
	```sql
  >>> SHOW TIME ZONE;
    TimeZone
  --------------
  Asia/Kolkata
  (1 row)

  >>> SELECT NOW();
                now
  ----------------------------------
  2025-12-30 13:13:41.555736+05:30
  (1 row)
	```
	Here you can see that, NOW() gives output in +05:30, which is offset for `Asia/Kolkata` timezone

-  To set a `TIME ZONE` more permanently, you can specify the `TIME ZONE` for the database
	```sql
  >>> ALTER DATABASE rgarg
  >>>   SET TIME ZONE 'UTC';
  ALTER DATABASE
	```

- You can set the timezone in the `postgresql` config file, location of which you can find as
	```sql
  >>> SHOW config_file ;
                config_file
  -----------------------------------------
  /etc/postgresql/16/main/postgresql.conf
  (1 row)
	```
	**NOTE** : the priority of which `TIME ZONE` will be followed is as follows : `session_timezone` > `database_timezone` > `postgresql_config_timezone`

- When converting any value without timezone to a value with timezone, `postgres` will assume the value in default timezone
	```sql
  >>> SELECT '31-12-2025 13:30:01'::TIMESTAMPTZ;
          timestamptz
  ---------------------------
  2025-12-31 13:30:01+05:30
  (1 row)
	```

- If you want output to be in a particular timezone, you don't need to set the session `TIME ZONE` for that. 

**NOTE** : If possible, always use `Named Timezone` (not `abbreviations`, not `hour offsets`) as it take care of daylight saving time (DST) which is not available in abbreviated timezone. Since the hour offsets accepts `POSIX-style` time zone specs, it has opposite meaning of sign as in `ISO-8601`, For example, the `POSIX` time zone for `2014-06-04 12:00+04:00` would be `UTC-4`.

```sql
>>> SET TIME ZONE 'UTC';
SET
>>> SELECT
  '2025-12-31 13:30:00'::TIMESTAMPTZ as UTC,
  '2025-12-31 13:30:00'::TIMESTAMPTZ at TIME ZONE 'Asia/Kolkata' as IST,
  '2025-12-31 13:30:00'::TIMESTAMPTZ at TIME ZONE 'IST' as IST_abbr,
  PG_TYPEOF('2025-12-31 13:30:00'::TIMESTAMPTZ at TIME ZONE 'Asia/Kolkata') as type,
  '2025-12-31 13:30:00'::TIMESTAMPTZ at TIME ZONE '+05:30' as positive_hour_offset,
  '2025-12-31 13:30:00'::TIMESTAMPTZ at TIME ZONE '-05:30' as negative_hour_offset,
  '2025-12-31 13:30:00'::TIMESTAMPTZ at TIME ZONE INTERVAL '+05:30' as interval_hour_offset;

          utc           |         ist         |      ist_abbr       |            type             | positive_hour_offset | negative_hour_offset | interval_hour_offset
------------------------+---------------------+---------------------+-----------------------------+----------------------+----------------------+----------------------
 2025-12-31 13:30:00+00 | 2025-12-31 19:00:00 | 2025-12-31 15:30:00 | timestamp without time zone | 2025-12-31 08:00:00  | 2025-12-31 19:00:00  | 2025-12-31 19:00:00
(1 row)
```
you can see here that in `positive_hour_offset` column, the time that should be `19:00`, it goes back to `08:00`, whereas opposite is true for `negative_hour_offset` column. To counter this situation you can use `INTERVAL`, which will take care of it as you see in example

- You can see all the `timezones` present in `PostgreSQL` with this
	```sql
  >>> SELECT * FROM pg_timezone_names;
                name               | abbrev | utc_offset | is_dst
  ----------------------------------+--------+------------+--------
  NZ-CHAT                          | +1345  | 13:45:00   | t
  Jamaica                          | EST    | -05:00:00  | f
  Antarctica/Davis                 | +07    | 07:00:00   | f
  Antarctica/Casey                 | +08    | 08:00:00   | f
  Antarctica/McMurdo               | NZDT   | 13:00:00   | t
  Antarctica/DumontDUrville        | +10    | 10:00:00   | f
  Antarctica/Troll                 | +00    | 00:00:00   | f
  Antarctica/Macquarie             | AEDT   | 11:00:00   | t
  Antarctica/Rothera               | -03    | -03:00:00  | f
  ...
  ...
  ...
	```
