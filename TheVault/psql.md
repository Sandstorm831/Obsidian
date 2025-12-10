- To list all important commands
```psql
\?
```
- To connect to a database `<DB_NAME>`
	```bash
psql -d <DB_NAME>
	```
- To auto format the output from the table so that it easily readable
	```psql
	\x auto
	```
- to find the current db
	```psql
	\c
	```
- to connect to a different db
```psql
\c <DATABASE_NAME>
```
- to list all databases
```psql
\l
```
- list all users
```sql
SELECT rolname FROM pg_roles;
-- another command
\du
```

- To delete/drop a user, follow these step
	- cannot delete a user that owns a database or has an active connection. Here we assume that it doesn't have an active connection. Let user be `xuser`
	- connect as `postgres` super user
		```bash
		sudo -iu postgres
		psql
		```
	- list all databases associated with `<USER>`
		```sql
		SELECT datname FROM pg_database WHERE datdba = (SELECT oid FROM pg_roles WHERE rolname='xuser');
		```
		- Outer Query
			- `pg_database`: system catalogue of all databases
			- `datname`: column name which contains the name of the database
			- `datdba`: owner of the db, stored as the user's `OID` in `pg_roles` table
		- Inner Query
			- `pg_roles`: system catalogue containing all PostgreSQL roles (users)
			- `oid`: object ID of that role, a unique id for PostgreSQL
			- `rolname`: name of the role
	- Now you can either change the ownership of the database ( assume db name be `xdb`) or drop the database itself
		```sql
		ALTER DATABASE xdb OWNER TO postgres -- change ownership
		DROP DATABASE xdb; -- delete/drop database
		```
	- now drop the user ( role )
		```sql
		DROP ROLE xuser;
		```