- `Interval` stores a duration of time
- It can be defined as a standard way of `[unit] [quantity] ...`
	```sql
  >>> SELECT '1 year 2 months 3 days 4 hours 5 minutes 6 seconds'::INTERVAL;
            interval
  -------------------------------
  1 year 2 mons 3 days 04:05:06
  (1 row)
	```
	It gives back an compact version of representing interval
	```sql
  >>> SELECT '1 year 2 months 3 days 04:05:06'::INTERVAL;
            interval
  -------------------------------
  1 year 2 mons 3 days 04:05:06
  (1 row)
	```

- Like `DateStyle`, `IntervalStyle` variable controls the output format of the `Interval`
	```sql
  >>> SHOW IntervalStyle;
  IntervalStyle
  ---------------
  postgres
  (1 row)

  >>> SET IntervalStyle = 'iso_8601';
  SET

  >>> SELECT '1 year 2 months 3 days 4 hours 5 minutes 6 seconds'::INTERVAL;
      interval
  ----------------
  P1Y2M3DT4H5M6S
  (1 row)
	```

- Now `ISO-8601` intervals have the following format
	- starts with a `P`, then 3 abbreviations `Y, M, D` for `Year`, `Month` and `Day`, separated with time Interval by a `T`, then 3 abbreviations `H, M, S` for `Hour`, `Minute` and `Seconds`, Ex: `P1Y2M3DT4H5M6S`
	- for only `hour`, `minute` and `second` intervals, `Y, M & D` abbreviations are removed, Ex: `PT4H5M6S`
		```sql
	  >>> SELECT '5 minutes 6 seconds'::INTERVAL;
    interval
    ----------
    PT5M6S
    (1 row)
		```


- There's an alternate ISO format
	- It starts with P, followed by 4 digit year, 2 digit month, 2 digit date, T, 2 digit Hour, 2 digit minute and at last 2 digit second, ex: `P0001-02-03T04:05:06`
		```sql
	 >>> SELECT 'P0001-02-03T04:05:06'::INTERVAL;
        interval
    ----------------
    P1Y2M3DT4H5M6S
    (1 row)
		```

- You can also define `interval` in discrete individual time formats like this
	```sql
  >>> SELECT INTERVAL '600' DAY;
  interval
  ----------
  600 days
  (1 row)

  >>> SELECT INTERVAL '40' MONTH;
      interval
  ----------------
  3 years 4 mons
  (1 row)

  >>> SELECT INTERVAL '500' MINUTE;
  interval
  ----------
  08:20:00
  (1 row)
	```
