- syntax for inserting checks, `CHECK (<CONDITION>)`
	```sql
  >>> CREATE TABLE check_example(
	price NUMERIC CONSTRAINT price_must_be_possitive CHECK ( price > 0 ),
	abbr TEXT
	);
	
	CREATE TABLE
	
  >>> INSERT INTO check_example (price, abbr) VALUES (-1, 'hell');
	ERROR:  new row for relation "check_example" violates check constraint "price_must_be_possitive"
	DETAIL:  Failing row contains (-1, hell).
	```

- To give a name to the check constraint, `CONSTRAINT` keyword is used followed by the desire name, you can see the above example for a better understanding
- The `CHECK` constraints we introduced are `column constraints`, that operate on the `column` only, they are written right next to the type of the `column`. We can apply constraints on `table` level too
- All the `column constraints` can be written as `Table constraint` but not all the `table constraints` can be written as `column constraint`.
	```sql
  >>> CREATE TABLE check_example(
  price NUMERIC CONSTRAINT price_must_be_positive CHECK ( price > 0 ),
  discount_price NUMERIC CHECK ( discount_price > 0 ),
  abbr TEXT,
  CHECK ( LENGTH(abbr) = 5),
  CHECK ( price > discount_price )
  );
  CREATE TABLE
  >>> INSERT INTO check_example (price, discount_price, abbr) VALUES (3, 1, 'foooo');
  INSERT 0 1
  >>>  INSERT INTO check_example (price, discount_price, abbr) VALUES (3, 4, 'foooo');
  ERROR:  new row for relation "check_example" violates check constraint "check_example_check"
  DETAIL:  Failing row contains (3, 4, foooo).
	```
	Here in the example we introduced two table constraint. **1st** is enforcing length of abbr entries to be equal to `5` and **2nd** is enforcing price to greater than discount_price. On adding conflicting values then we got the error

- check contraints that have more than 1 column involved should be made as a table constraint
- data-integrity rules should be enforced on the database and not business logic, like `price > 0` seems to be a data-integrity check and not a business logic
- `CHECK` constraint can't reference other table, it can't reference another row besides the one which is currently being updated or created
- you have to drop and recreate the check constraint, you can't alter the logic of the check constraint though you can alter the name of the check constraint.