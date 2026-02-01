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