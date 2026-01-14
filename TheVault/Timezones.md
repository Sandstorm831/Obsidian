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