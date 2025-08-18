- Concurrency
	1. Concurrent Writes is not supported
	2. Read operations during a write operation is also not supported.
	3. Solved by using Mutex type locking mechanisms but have to be implemented by yourself.
	

- Python SQLite3 Code example
	```python
	# Standard syntax and their use
	import sqlite3
	#To connect to the DB
	connection = sqlite3.connect("Path/to/your/sqlite.db")
	# adding Row class, it supports iteration amd mapping
	# by column_name and index
	connection.row_factory = sqlite3.Row
	# To get the cursor
	# Cursor in a general sense, that help to run 
	# queries in iterative ways over the DB
	cursor = connection.cursor()
	
	# To execute a query, get a sql query string
	# can be parametrized with `?` and attach a 
	# tuple of parameters in cursor
	sql_query = 
		"""
		SELECT * from reports
		WHERE id = ?
		"""
	report_id = 1
	cursor.execute(sql_query, (report_id,))
	
	# If the query includes INSERT, DELETE, UPDATE, REPLACE
	# an transaction instance is created, so for your mutations
	# to take place, you have to call on connection object
	# i.e
	# connection.commit()
	
	# Now to get the result
	result = cursor.fetchall()
	# result is list of Row Class objects
	# you can convert the Row class objects to dict
	# via dict() function
	result_dict = [dict(row) for row in result]
	
	# if the operation includes mutations, then you can check
	# the number of rows affected by 
	# cursor.rowcount()
	
	# Close the connection
	connection.close()
	```

- ***SQLite doesn't support multiple update operations in a single query***, you have to make multiple queries for that. If your queries doesn't have high latency, than, making multiple update queries isn't going to make much difference, use `executemany` from sqlite3 in python to make multiple queries, and commit at last.

- `cursor.executemany()`
	```python
	data = [
		("hello", 1, 2),
		("duble",4,5),
		("mellow", 6,4)
	]
	cursor.executemany("INSERT INTO movie VALUES(?, ?, ?)", data)
	connection.commit()
	```

- row_factory method for getting result in a dict format
	```python
from sqlite3 import Cursor

def dict_factory(cursor: Cursor, row):
	fields = [col[0] for col in cursor.description] # list of field names in db
	return {key: val for key, val in zip(fields, row)}
	```
	usage
	```python
	from src.dict_factory import dict_factory
	import sqlite3
	
	conn = sqlite3.connect("path/to/db")
	connection.row_factory = dict_factory
	```