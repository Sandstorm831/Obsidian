- [[psql]]
---
- `ANY` and `IN` are Postgres constructs that can be used `WHERE` clause as follows:
	```python
	import psycopg
	connection = psycopg.connect(
		host=os.getenv("PG_DB_HOST", "localhost")
		port=os.getenv("PG_DB_PORT", 5432),
		dbname=os.getenv("PG_DB_DATABASE"),
		user=os.getenv("PG_DB_USER"),
		password=os.getenv("PG_DB_PASSWORD"),
	)
	param_ids = [1,2,3,4,5]
	cursor = connection.cursor()
	sql_query = """
	SELECT * FROM users
	WHERE id = ANY(%s);
	"""
	cursor.execute(sql_query, (param_ids))
	```
	if we look at the raw `SQL` Query, then there is an extra `ARRAY` expression which is automatically fixed by `psycopg` for us
	```sql
	SELECT * FROM users
	WHERE id = ANY(ARRAY[1,2,3,4,5]);
	```
	whereas, you can understand `IN` as equivalent to `= ANY`, and `ANY` is more efficient. 
	
	The case where both `IN` and `ANY` differ is [here](https://www.postgresql.org/docs/17/functions-comparisons.html) 
-  To generate series in Postgres, use this function
```sql
generate_series(inittial, final) --> function to generate series from initial value to final value
```

---

- A PostgreSQL Instance structure looks like this ![[postgres1.excalidraw]]
-  Default user of a `postgreSQL` instance is `postgres`, and it can be logged in via `psql` only when the `username` of the OS profile is also `postgres`
- To login for the first time, follow the steps
	- switch to `postgres` user
		```bash
		sudo -iu postgres
		```
	- login via simply command
		```bash
		psql
		```
	- make a user of the admin user with the same name as of original username of the OS profile
		```sql
		CREATE ROLE <USERNAME> WITH LOGIN PASSWORD '<PASSWORD>'; -- It will create a new user with <USERNAME> and <PASSWORD> 
		ALTER ROLE <USERNAME> SUPERUSER CREATEDB CREATEROLE; -- It will alleviate the USERNAME to super-user and also give the role to create DB and create roles
		```
	- by default when you login, `psql` needs a database with the same name as user, so we will create it now
		```sql
		CREATE DATABASE <USERNAME> OWNER <USERNAME>;
		```
	- now you exit the `psql` and exit the `postgres` user and to the original user. Now you are able to login to the `postgres` with `psql` command


- [[Postgres_Numeric_Types]]
- [[Postgres_Character_types]]
- [[Check_Constraint]]
- [[Domains]]
- [[Character_sets_and_collations]]
- [[Binary_data]]