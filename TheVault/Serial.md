- Not a real `data-type`, it uses `integer` and `sequences` under the hood but also does 2-3 other things
- Most often, it is used as `Auto-Incrementing Primary Key`. After `Postgres 10`, `Serial` is not the preferred way to create an `auto-incrementing primary key`
- Here's how it works under the hood
	```sql
  >>> CREATE TABLE serial_example(
  >>> id SERIAL
  >>> );

  -- It equivalently expands to

  >>> CREATE SEQUENCE serial_example_id_seq AS INTEGER;
  >>> CREATE TABLE serial_example (
  >>> id INTEGER NOT NULL DEFAULT nextval('serial_example_id_seq')
  >>> );
  >>> ALTER SEQUENCE serial_example_id_seq OWNED BY serial_example.id;
	```
	The `ALTER` statement at the end is used to transfer the ownership of the `sequence` to the `id` column of the `serial_example` table, so if the table or the column is `dropped`, the `sequence` is automatically destroyed too.


- If you want to continue using `SERIAL` as your primary-key, use `BIG-SERIAL` instead. Unlike `SERIAL`, it's based on `BIGINT` instead of `INTEGER`.
- For primary keys, be generous with space as you don't want to run out of space for primary keys.
- You could end up with gaps in a `sequence` as the internal pointer doesn't move forward in a transaction safe way. So if any transaction that pulled a value gets `rolled-back` or can't be completed then you'll have a gap.
- You have to declare it as a `PRIMARY KEY` if you have to make it primary key.