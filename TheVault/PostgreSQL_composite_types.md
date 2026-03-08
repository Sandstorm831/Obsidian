> **NOTE : Composite types isn't what you are looking for most probably, Composite types has a very few use cases and in all probability you don't need that.**

• Composite types represents the structure of row, it essentially a list of field names and their data-types.
• fields in composite types can't have any kind of constraint.

```sql
  >>> CREATE TYPE addr as (
  >>> number text,
  >>> street text,
  >>> city text,
  >>> state text,
  >>> postal text
  >>> );
  CREATE TYPE

  >>> SELECT ROW('123', 'main st', 'nowTown', 'yourState', '13183')::addr;
                    row
  -----------------------------------------
  (123,"main st",nowTown,yourState,13183)
  (1 row)

  >>> SELECT ('123', 'main st', 'nowTown', 'yourState', '13183')::addr;
                    row
  -----------------------------------------
  (123,"main st",nowTown,yourState,13183)
  (1 row)

  >>> SELECT pg_typeof( ROW('123', 'main st', 'nowTown', 'yourState', '13183')::addr);
  pg_typeof
  -----------
  addr
  (1 row)

```


• You can create a table with one or more rows with addr type
```sql
>>> CREATE TABLE addresses (
>>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
>>>   address addr
>>> );
CREATE TABLE

>>> INSERT INTO addresses (address) VALUES (ROW('123', 'main st', 'nowTown', 'yourState', '13183'));
INSERT 0 1
>>> SELECT * FROM addresses;
 id |                 address
----+-----------------------------------------
  1 | (123,"main st",nowTown,yourState,13183)

• You can access the individual field from the composite type by enclosing it in parenthesis
Example

>>> SELECT id, address.state FROM addresses;
ERROR:  missing FROM-clause entry for table "address"
LINE 1: SELECT id, address.state FROM addresses;
                   ^
>>> SELECT id, (address).state FROM addresses;
 id |   state
----+-----------
  1 | yourState
(1 row)

```

