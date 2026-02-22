- `Postgres` allows columns to be defined as `variable-length` `multidimensional` array
- Array can be created by any `built-in` or `user-defined` base types
	```sql
  >>> CREATE TABLE array_example(
  >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>> int_array INTEGER[],
  >>> text_array TEXT[],
  >>> bool_array BOOLEAN[],
  >>> nested_array INTEGER[][]
  >>> );
  CREATE TABLE
	```
	array type is named by appending square bracket to the data-type
	```sql
  >>> INSERT INTO array_example
  >>>   (int_array, text_array, bool_array)
  >>> VALUES
  >>>   (
  >>>     ARRAY [1,2,3,4],
  >>>     '{"marigold", "daisy", "poppy", "sunflower"}',
  >>>     ARRAY [true, false, false, true]
  >>>   );
  INSERT 0 1

  >>> INSERT INTO array_example
  >>>   (nested_array)
  >>> VALUES
  >>>  ('{{1,2,3}, {4,5,6}, {7,8,9}}');
  INSERT 0 1

  >>> SELECT * FROM array_example;
  id | int_array |            text_array            | bool_array |       nested_array
  ----+-----------+----------------------------------+------------+---------------------------
    1 | {1,2,3,4} | {marigold,daisy,poppy,sunflower} | {t,f,f,t}  |
    2 |           |                                  |            | {{1,2,3},{4,5,6},{7,8,9}}
  (2 rows)
	```
	You can either use `'{}'` or `ARRAY []` to write arrays, as you can see in the example

- A few array operators
	1. accessing elements of array is done by `1-based indexing` while for arrays inside `json` it's still `0-based indexing`
		```SQL
		>>> SELECT
       >>>   id, text_array
       >>> FROM
       >>>   array_example;
       id |            text_array
       ----+----------------------------------
           1 | {marigold,daisy,poppy,sunflower}
           2 |
       (2 rows)

       >>> SELECT
       >>>   id, text_array[1]
       >>> FROM
       >>>   array_example;
       id | text_array
       ----+------------
           1 | marigold
           2 |
       (2 rows)
		```
	2. You can also do slices
		```sql
        >>> SELECT
        >>>   id, text_array[2:]  -- all array elements including and beyond 2nd element
        >>> FROM
        >>>   array_example;
        id |       text_array
        ----+-------------------------
        1 | {daisy,poppy,sunflower}
        2 |
        (2 rows)

        >>> SELECT
        >>>   id, text_array[2:3]  -- all array elements between 2nd and 3rd element, including 2nd and 3rd
        >>> FROM
        >>>   array_example;
        id |  text_array
        ----+---------------
        1 | {daisy,poppy}
        2 |
        (2 rows)

        >>> SELECT
        >>>   id, text_array[:3]  -- all array elements upto 3rd element,including 3rd
        >>> FROM
        >>>   array_example;
        id |       text_array
        ----+------------------------
        1 | {marigold,daisy,poppy}
        2 |
        (2 rows)

        >>> SELECT
        >>>   id, text_array[:]   -- all array elements
        >>> FROM
        >>>   array_example;
        id |            text_array
        ----+----------------------------------
        1 | {marigold,daisy,poppy,sunflower}
        2 |
        (2 rows)

        >>> SELECT
        >>>   id, text_array[:24323]  -- if length is smaller then given number, than also it will give full-length of array
        >>> FROM
        >>>   array_example;
        id |            text_array
        ----+----------------------------------
        1 | {marigold,daisy,poppy,sunflower}
        2 |
        (2 rows)

		```
	3. **Array Includes Operator** : `@>` , It checks if the given is included in the array
		```sql
        >>> SELECT
        id, text_array
        FROM
        array_example
        WHERE
        text_array @> ARRAY['poppy'];
        id |            text_array
        ----+----------------------------------
        1 | {marigold,daisy,poppy,sunflower}
        (1 row)


        >>> SELECT
        id, text_array
        FROM
        array_example
        WHERE
        text_array @> ARRAY['poppysaf'];
        id | text_array
        ----+------------
        (0 rows)

        >>> SELECT
        id, text_array
        FROM
        array_example
        WHERE
        text_array @> '{"poppy"}';
        id |            text_array
        ----+----------------------------------
        1 | {marigold,daisy,poppy,sunflower}
        (1 row)
		```
	4. **Un-nest operator** : Takes an array and turns it into a result set, and the columns which are are not array are simply copied down. Follow example for a good understanding
		```sql
        >>> SELECT
        >>>   id, unnest(text_array)
        >>> FROM
        >>>   array_example;
        id |  unnest
        ----+-----------
        1 | marigold
        1 | daisy
        1 | poppy
        1 | sunflower
        (4 rows)
		```