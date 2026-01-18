- Don't use `Date & Time` as separate columns if you have to store both date and time, Use `Timestamp` or `TimestampTZ`
- Along side `Time` there's `TimeTZ` data-type which stores time with timezone and makes 0 sense. ***Don't Use it EVER***.
- For `Date`, only thing you have to look out for is the ambiguous Date notation. Which is disambiguated by the `DateStyle` variable
	```sql
  >>> SELECT '2024-04-03'::DATE;
      date
  ------------
  2024-04-03
  (1 row)

  >>> SELECT '2024/04/03'::DATE;
      date
  ------------
  2024-04-03
  (1 row)

  >>> SHOW datestyle ;
  DateStyle
  -----------
  ISO, DMY
  (1 row)
	```
	As you can see, since `DateStyle` has `DMY`, the ambiguous date of `2024/04/03` is disambiguated to `2024-04-03`

- For `Time`, you can add the precision for fractional seconds ranging from `1` to `6`. Default `TIME` has full precision of `6`, you can specify a precision of less than that and the rest will get truncated
	```sql
  >>> SELECT '13:43:23.2'::TIME;
      time
  ------------
  13:43:23.2
  (1 row)

  >>> SELECT '13:43:23.2342'::TIME(2);
      time
  -------------
  13:43:23.23
  (1 row)

  >>> SELECT '13:43:23.2342'::TIME;
      time
  ---------------
  13:43:23.2342
  (1 row)

  >>> SELECT NOW()::TIME;
        now
  -----------------
  06:29:59.756617
  (1 row)
	```

- There are some fun string literals you can be used
	```sql
  >>> SELECT 'allballs'::TIME;
    time
  ----------
  00:00:00
  (1 row)
  >>> -- it gives all zeros

  >>> SELECT 'epoch'::TIMESTAMP;
        timestamp
  ---------------------
  1970-01-01 00:00:00
  (1 row)

  >>> SELECT 'epoch'::TIMESTAMPTZ;
        timestamptz
  ------------------------
  1970-01-01 00:00:00+00
  (1 row)

  >>> SELECT 'epoch'::DATE;
      date
  ------------
  1970-01-01
  (1 row)
	```
	`epoch` is a point a real `datetime` point in the `timeline`, so it can't be converted to just `time`, although it can be converted to `date`
	```sql
  >>> SELECT 'TOMORROW'::TIMESTAMPTZ;
        timestamptz
  ------------------------
  2026-01-01 00:00:00+00
  (1 row)

  >>> SELECT 'TOMORROW'::TIMESTAMP;
        timestamp
  ---------------------
  2026-01-01 00:00:00
  (1 row)

  >>> SELECT 'TOMORROW'::DATE;
      date
  ------------
  2026-01-01
  (1 row)

  >>> SELECT 'YESTERDAY'::TIMESTAMPTZ;
        timestamptz
  ------------------------
  2025-12-30 00:00:00+00
  (1 row)

  >>> SELECT 'YESTERDAY'::TIMESTAMP;
        timestamp
  ---------------------
  2025-12-30 00:00:00
  (1 row)

  >>> SELECT 'YESTERDAY'::DATE;
      date
  ------------
  2025-12-30
  (1 row)
	```
	both `tomorrow` and `yesterday` are also real `datetime` point in the `timeline`, so it can't be converted to just time

- It is advised ***NOT*** to use `tomorrow` and `yesterday`strings for any stored procedure or function definition as it could be converted to it's actual literal value and it can become stale. Instead you should use `CURRENT_DATE + 1` for `tomorrow` and `CURRENT_DATE - 1` for `yesterday
	```sql
  >>> SELECT CURRENT_DATE;
  current_date
  --------------
  2025-12-31
  (1 row)

  >>> SELECT CURRENT_DATE + 1;
    ?column?
  ------------
  2026-01-01
  (1 row)

  >>> SELECT CURRENT_DATE - 1;
    ?column?
  ------------
  2025-12-30
  (1 row)
	```


