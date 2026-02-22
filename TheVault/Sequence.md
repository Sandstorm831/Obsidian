-  `CREATE SEQUENCE my_sq as BIGINT;` creates a new `sequence` number generator named `my_sq` with `BIGINT` as underlying `datatype`. It can take several other options
	- `INCREMENT 1` : It can change the increment of the sequence, default is 1
	- START 1 : It can change the starting point of the sequence, default values is `minvalue` for ascending and `maxvalue` for descending
	- `MINVALUE 1` : determines the min value sequence can generate, default for ascending is 1 and for descending it is min-value of data-type
	- `MAXVALUE 100` : determines the max value sequence can generate, default for descending is -1 and for ascending it is max-value of data-type
	 - `CACHE 1` : how many sequences values to cache before pulling out new one. default is 1
	```sql
  >>> CREATE SEQUENCE my_sq
  >>> AS BIGINT
  >>> INCREMENT 1
  >>> START 1
  >>> MINVALUE 1
  >>> CACHE 1;
  CREATE SEQUENCE
	```

- `nextval('my_sq')` : advances the `sequence` and gives back new value regardless of concurrent sessions
	```sql
  >>> SELECT nextval('my_sq');
  nextval
  ---------
        1
  (1 row)

  >>> SELECT nextval('my_sq');
  nextval
  ---------
        2
  (1 row)
	```

- `currval('my_sq')` : returns the value most recently obtained by the `nextval` for the sequence in the **CURRENT SESSION**, error will be called if `nextval` is never called for this sequence in **this session**.
	```sql
  >>> SELECT nextval('my_sq');
  nextval
  ---------
        8
  (1 row)

  >>> SELECT currval('my_sq');
  currval
  ---------
        8
  (1 row)

  -- in a new session

  ❯ psql

  >>> SELECT currval('my_sq');
  ERROR:  currval of sequence "my_sq" is not yet defined in this session
	```

- `setval('my_sq', 8)`: Set the sequence to the given number.
	```sql
  >>> SELECT setval('my_sq', 3);
  setval
  --------
        3
  (1 row)

  >>> SELECT currval('my_sq');
  currval
  ---------
        3
  (1 row)

  >>> SELECT nextval('my_sq');
  nextval
  ---------
        4
  (1 row)
	```
	