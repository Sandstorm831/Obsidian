- You can store raw `bits` and `bytes` in `postgres`, most of the time it's a bad idea but sometimes it can be good
- it's syntax is `BYTEA`
- It's varying size column, can store `1GB` of data in one row of this column. Large data are `TOASTed`, that is broken down in other rows, just like in `TEXT` data-type
- If you need to store files, use file-storage like `S3` and leave a `pointer` or `URL` in the database
- You can store `checksums` of the files in the database, it makes checking and comparing very fast as compared to comparing files
	```sql
  >>> CREATE TABLE byte_example(
  >>> file_name TEXT,
  >>> data BYTEA
  >>> );
  CREATE TABLE
  
  >>> INSERT INTO byte_example (file_name, data) VALUES ('hello.txt','\x48656c6c6f20576f726c64');
  INSERT 0 1
  >> -- \x48656c6c6f20576f726c64 -> hex representation of 'Hello World', \x defines that is is binary
  >>>  SELECT * FROM byte_example ;
   file_name |           data
  -----------+--------------------------
   hello.txt | \x48656c6c6f20576f726c64
  (1 row)
	```
	
- You see the output of data column is Hex, it is the default and you should not change this.
- `bytea_output` is the setting that controls what the `CLIENT` gives you
	```sql
  >>> SHOW bytea_output;
   bytea_output
  --------------
   hex
  (1 row)
	```
	Here it is set to 'hex', if you change it to 'escape', the client will try to convert the hex into characters but not all hex can be represented as characters, so you better not do that and stick to 'hex'
	```sql
  >>> SELECT * from byte_example ;
   file_name |           data
  -----------+--------------------------
   hello.txt | \x48656c6c6f20576f726c64
  (1 row)
  
  >>> set bytea_output = 'escape';
  SET
  >>> SHOW bytea_output;
   bytea_output
  --------------
   escape
  (1 row)
  
  >>> SELECT * FROM byte_example ;
   file_name |    data
  -----------+-------------
   hello.txt | Hello World
  (1 row)
	```

- A simple `md5` hash of a string can be calculated as follows
	```sql
  >>> SELECT md5('hello world');
                 md5
  ----------------------------------
   5eb63bbbe01eeed093cb22bb8f5acdc3
  (1 row)
	```
	`MD5` is not secure, do not use `md5` anywhere security, cryptographic security, password etc.

- Super fast to calculate digests and hashes. Can use that for strict `equality lookups` as it's `superfast`
- default output of `md5` is `TEXT`, whereas, default output of `sha256` is a `byte-array`
	```sql
  >>> SELECT pg_typeof(md5('hello world'));
   pg_typeof
  -----------
   text
  (1 row)

  >>> SELECT pg_typeof(sha256('hello world'));
   pg_typeof
  -----------
   bytea
  (1 row)
	```

- You can convert the `md5` to a `bytea` output
	```sql
  >>> SELECT pg_typeof(decode(md5('hello world'), 'hex'));
   pg_typeof
  -----------
   bytea
  (1 row)
	```

- There's a little `bytea` storage overhead, so if you check the size of the column, you will get more than `16 bytes` (`128 bit` output size of `md5`) as opposed to `16 byte` output of `md5` hash
	```sql
  >>> SELECT pg_column_size(decode(md5('hello world'), 'hex'));
   pg_column_size
  ----------------
               20
  (1 row)
	```

- `bytea` (hex) output of `md5` is considerably smaller as compared to the default `TEXT` output
	```sql
  >>> SELECT pg_column_size(decode(md5('hello world'), 'hex')), pg_column_size(md5('hello world'));
  pg_column_size | pg_column_size
  ----------------+----------------
              20 |             36
  (1 row)
	```

- We can cast `md5` hash to `UUID` (both are `16 bytes` long), this actually saves `4 byte` of space imposed by `BYTEA` storage overheads
	```sql
  >>> SELECT decode(md5('hello world'), 'hex');
                decode
  ------------------------------------
  \x5eb63bbbe01eeed093cb22bb8f5acdc3
  (1 row)

  >>> SELECT md5('hello world')::uuid;
                  md5
  --------------------------------------
  5eb63bbb-e01e-eed0-93cb-22bb8f5acdc3
  (1 row)
	```
you can see dashes are introduced after casting it into `UUID` but overall it's same
  ```sql
  >>> SELECT pg_column_size(md5('hello world')::uuid);
   pg_column_size
  ----------------
               16
  (1 row)

  >>> SELECT pg_column_size(decode(md5('hello world'), 'hex'));
   pg_column_size
  ----------------
               20
  (1 row)
  ```

- So you can see `UUID` is smaller representation of `md5` hash
	```sql
  >>> SELECT pg_column_size(md5('hello world')::uuid),
  >>>     pg_column_size(decode(md5('hello world'), 'hex')),
  >>>     pg_column_size(md5('hello world'));
  pg_column_size | pg_column_size | pg_column_size
  ----------------+----------------+----------------
              16 |             20 |             36
  (1 row)
	```
