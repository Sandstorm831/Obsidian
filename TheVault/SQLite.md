- Concurrency
	1. Concurrent Writes is not supported
	2. Read operations during a write operation is also not supported.
	3. Solved by using Mutex type locking mechanisms but have to be implemented by yourself.
	

- Code
	```python
	# Standard syntax and their use
	import sqlite3
	#To connect to the DB
	connection = sqlite3.connect("Path/to/your/sqlite.db")
	
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
	
	# if the operation is mutations, then you can check
	# the number of rows affected by 
	# cursor.rowcount()
	
	# Close the connection
	connection.close()
	```
