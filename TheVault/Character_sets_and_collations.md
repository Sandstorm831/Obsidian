#### 1. Character Set
- Character Set which is also called `Charset`
- Encoding defines the way how we go from `characters` written on screen to `bytes` stored on disk
- `Charset` defines what the legal characters are
- if a character is not present in that particular charset you are using, you will get an error
- `UTF-8` : multi-byte encoding and ranges from 1 to 4 bytes
	- supports accented characters, upper - lower case, entire range of `Unicode` and include `emojis`
	- extremely versatile


#### 2. Collations
- Set of rules how the characters are related to each other like `a` & `A`


**Note** : There's a server encoding that `postgres` use, and there is a `client` (through which you connect to `postgres`) encoding. If they are not same then `postgres` will attempt to convert server encoded data to client encoding, but there can be some characters that misbehaves

Example
```sql
>>>  \l
                                                  List of databases
   Name    |  Owner   | Encoding | Locale Provider | Collate | Ctype | ICU Locale | ICU Rules |   Access privileges
-----------+----------+----------+-----------------+---------+-------+------------+-----------+-----------------------
 first_db  | postgres | UTF8     | libc            | en_IN   | en_IN |            |           |
 postgres  | postgres | UTF8     | libc            | en_IN   | en_IN |            |           |
 rgarg     | rgarg    | UTF8     | libc            | en_IN   | en_IN |            |           |
 template0 | postgres | UTF8     | libc            | en_IN   | en_IN |            |           | =c/postgres          +
           |          |          |                 |         |       |            |           | postgres=CTc/postgres
 template1 | postgres | UTF8     | libc            | en_IN   | en_IN |            |           | =c/postgres          +
           |          |          |                 |         |       |            |           | postgres=CTc/postgres
(5 rows)

>>>  show client_encoding;
 client_encoding
-----------------
 UTF8
(1 row)
```
Here our `client_encoding` and `encodings` of all our tables are same. Here `psql` is being used as client