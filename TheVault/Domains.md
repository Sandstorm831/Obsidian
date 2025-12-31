- It takes the `data-type` and the `constraint` and combine them into single custom name. It is a combination of `type` and `constraint`
- `domain` can only reference a single column
- `domain` can be altered
	```sql
  >>> CREATE DOMAIN us_postal_code as TEXT CONSTRAINT postal_format CHECK ( VALUE ~ '^\d{5}$' OR VALUE ~ '^\d{5}-\d{4}$' );
  CREATE DOMAIN
  >>> CREATE TABLE domain_ex(
  >>> street TEXT NOT NULL,
  >>> city TEXT NOT NULL,
  >>> postal us_postal_code NOT NULL
  >>> );
  CREATE TABLE
  >>>  INSERT INTO domain_ex (street, city, postal) VALUES ('main', 'dallas', '7343');
  ERROR:  value for domain us_postal_code violates check constraint "postal_format"
  >>>  INSERT INTO domain_ex (street, city, postal) VALUES ('main', 'dallas', '73433');
  INSERT 0 1
  >>>  INSERT INTO domain_ex (street, city, postal) VALUES ('main', 'dallas', '73433-2');
  ERROR:  value for domain us_postal_code violates check constraint "postal_format"
  >>>  INSERT INTO domain_ex (street, city, postal) VALUES ('main', 'dallas', '73433-2234');
  INSERT 0 1
  >>>  \d domain_ex
                   Table "public.domain_ex"
   Column |      Type      | Collation | Nullable | Default
  --------+----------------+-----------+----------+---------
   street | text           |           | not null |
   city   | text           |           | not null |
   postal | us_postal_code |           | not null |
  
  >>>  SELECT * FROM domain_ex ;
   street |  city  |   postal
  --------+--------+------------
   main   | dallas | 73433
   main   | dallas | 73433-2234
  (2 rows)
	```