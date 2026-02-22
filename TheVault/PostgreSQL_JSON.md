- When deciding whether you should put `JSON` in `postgres` or break it down to `top-level columns`, then you should consider a few rules of thumb none of them are strict but a good starting point
	1. If you are needing to query constantly inside the `JSON` document then it's a good indicator that you should break it down to top-level columns. Example: If **email** is one of the key in `JSON` document that you query to identify the user, you should break it down to `top-level column`
	2. If it is a rigid, well-defined schema, than you should consider breaking it down to individual `top-level columns` especially when each key is updated independently. You should consider it keeping it as a `JSON blob` if the whole blob is updated all at once.
	3. If you are getting massive `JSON documents`, you should consider breaking it down to individual `top-level columns` or smaller `json` columns as storing massive `JSON Documents` could tank the performance.
- Now, let's consider the difference between `JSON` and `JSONB` data-types
	```sql
  >>> SELECT '1'::JSON;
  json
  ------
  1
  (1 row)

  >>> SELECT '1'::JSONB;
  jsonb
  -------
  1
  (1 row)

  >>> SELECT pg_column_size('1'::JSON) as JSON, pg_column_size('1'::JSONB) as JSONB;
  json | jsonb
  ------+-------
      5 |    20
  (1 row)
	```
	now we can see that `JSONB` takes far more space as compared to `JSON` so we should use `JSON`, but `JSON` stores the input in `TEXT` under-the-hood whereas `JSONB` parses the given input and stores it in binary format and keep around some extra bytes which makes querying and updating faster which accounts for the extra bigger size. So it's advised to use `JSONB`

- size difference between `json` and `jsonb` get reduced as you start inserting bigger `json` objects as the overhead gets amortised over the entire object
	```sql
  >>> SELECT
  >>>   pg_column_size('{"a": "Hello World"}'::json) as json,
  >>>   pg_column_size('{"a": "Hello World"}'::jsonb) as jsonb;
  json | jsonb
  ------+-------
    24 |    28
  (1 row)
	```

- Whatever valid `json` you give to the `JSON`, it will get stored as it is but `JSONB` parses and store it as binary representation so it only stores the relevant information.
	```sql
  >>> SELECT
  >>>   '{"a":       "Hello World"}'::json as json,
  >>>   '{"a":       "Hello World"}'::jsonb as jsonb;
              json            |        jsonb
  ----------------------------+----------------------
  {"a":       "Hello World"} | {"a": "Hello World"}
  (1 row)
	```
	here the white space between `key` and `value` is retained in the `JSON` but not in `JSONB`. Similarly you can't have duplicate keys in `json` so `JSONB` remembers that spec and overwrites with the new key-value pair whereas `JSON` doesn't. `JSON` will throw an error if given invalid `JSON`.

- `json` spec itself doesn't assure any retaining of initial ordering of the keys, so `JSONB` can reorder keys however as `JSON` store the input in `TEXT` under the hood it will retain whatever ordering of keys you gave it.
	```sql
  >>> SELECT 
  '{"c": "3", "a": "1", "b": "2"}'::JSON as JSON ,
  '{"c": "3", "a": "1", "b": "2"}'::JSONB as JSONB;
                json              |             jsonb              
  --------------------------------+--------------------------------
  {"c": "3", "a": "1", "b": "2"} | {"a": "1", "b": "2", "c": "3"}
  (1 row)
	```

- Here how you can query data from `JSONB` objects
	```sql

  >>> SELECT '
  >>>   {
  >>>     "string": "Hello World",
  >>>     "number": 42,
  >>>     "boolean": true,
  >>>     "null": null,
  >>>     "array": [ 1,2,3],
  >>>     "object": {
  >>>       "key": "value"
  >>>     }
  >>>   }
  >>> '::jsonb -> 'string';
    ?column?
  ---------------
  "Hello World"
  (1 row)

  -- current output is quoted, you can remove the quotes as follows

  >>> SELECT '
  >>>   {
  >>>     "string": "Hello World",
  >>>     "number": 42,
  >>>     "boolean": true,
  >>>     "null": null,
  >>>     "array": [ 1,2,3],
  >>>     "object": {
  >>>       "key": "value"
  >>>     }
  >>>   }
  >>> '::jsonb ->> 'string';
    ?column?
  -------------
  Hello World
  (1 row)

  -- You can exact deep-nested objects also

  >>> SELECT '
  >>>   {
  >>>     "string": "Hello World",
  >>>     "number": 42,
  >>>     "boolean": true,
  >>>     "null": null,
  >>>     "array": [ 1,2,3],
  >>>     "object": {
  >>>       "key": "value"
  >>>     }
  >>>   }
  >>> '::jsonb -> 'object' ->> 'key';
  ?column?
  ----------
  value
  (1 row)

  -- You can access the array elements also

  >>> SELECT '
  >>>   {
  >>>     "string": "Hello World",
  >>>     "number": 42,
  >>>     "boolean": true,
  >>>     "null": null,
  >>>     "array": [ 1,2,3],
  >>>     "object": {
  >>>       "key": "value"
  >>>     }
  >>>   }
  >>> '::jsonb -> 'array' ->> 0;
  ?column?
  ----------
  1
  (1 row)
	```