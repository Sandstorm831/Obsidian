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
	