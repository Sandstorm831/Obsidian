- There are two `data-types` provided by `postgres` that supports full text-search : `tsvector` & `tsquery`
- `tsvector` type represents a document in a form optimised for text search
- `tsquery` similarly represents a text query
	```sql
  >>> SELECT to_tsvector('the quick brown fox jumps over the lazy dog');
                        to_tsvector
  -------------------------------------------------------
  'brown':3 'dog':9 'fox':4 'jump':5 'lazi':8 'quick':2
  (1 row)

  >>> SELECT pg_typeof(to_tsvector('the quick brown fox jumps over the lazy dog'));
  pg_typeof
  -----------
  tsvector
  (1 row)
	```
	The `output` of function `to_tsvector` is a map of `lexemes` and the positions of corresponding full-words in the sentence

- a `tsvector` is defined as a sorted vector of distinct `lexemes`  (**`lexeme` is a normalised form of word that text search system indexes and searches**)
	```sql
  >>> SELECT to_tsvector('the quick brown fox jumps jumping over the lazy laziness dog');
                          to_tsvector
  -------------------------------------------------------------
  'brown':3 'dog':11 'fox':4 'jump':5,6 'lazi':9,10 'quick':2
  (1 row)
	```
	adding `laziness` and `jumping` does add additional position in the vector for `jump` and `lazi` but doesn't add any new word

- `tsquery` is used to search against `tsvector`
	```sql
  >>> SELECT to_tsquery('lazy');
  to_tsquery
  ------------
  'lazi'
  (1 row)

  >>> SELECT pg_typeof(to_tsquery('lazy'));
  pg_typeof
  -----------
  tsquery
  (1 row)
	```

- We can take your search string, convert it to a `ts_query` and then apply that against our `tsvector`. We can use `@@` text search match operator which tests whether a `tsvector` matches a `tsquery`
	```sql
  >>> SELECT to_tsvector('the quick brown fox jumps over the lazy dog') @@ to_tsquery('lazy');
  ?column?
  ----------
  t
  (1 row)

  >>> SELECT to_tsvector('the quick brown fox jumps over the lazy dog') @@ to_tsquery('laziness');
  ?column?
  ----------
  t
  (1 row)

  >>> SELECT to_tsvector('the quick brown fox jumps over the lazy dog') @@ to_tsquery('foxy');
  ?column?
  ----------
  f
  (1 row)

  >>> SELECT to_tsvector('the quick brown fox jumps over the lazy dog') @@ to_tsquery('fox');
  ?column?
  ----------
  t
  (1 row)
	```

- You can specify the language for the `to_tsvector` function, default value from `default_text_search_config` variable is used if none is specified
	```sql
  >>> show default_text_search_config ;
  default_text_search_config
  ----------------------------
  pg_catalog.english
  (1 row)

  >>> SELECT to_tsvector('english', 'the quick brown fox jumps over the lazy dog');
                        to_tsvector
  -------------------------------------------------------
  'brown':3 'dog':9 'fox':4 'jump':5 'lazi':8 'quick':2
  (1 row)
	```

- You can have a `Generated column` where you can store the `tsvector` of the text you are storing in the table
	```sql
  >>> CREATE TABLE ts_example(
  >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>> content text,
  >>> search_vector_en TSVECTOR GENERATED ALWAYS AS (to_tsvector(content)) STORED
  >>> );
  ERROR:  generation expression is not immutable
  
  -- Here error says generation expression is not immutable, it is because we have not specified language
  -- and changing default_text_search_config will change the output, so we just have to specify the language
  
  
  >>> CREATE TABLE ts_example(
  >>>   id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  >>>   content text,
  >>>   search_vector_en TSVECTOR GENERATED ALWAYS AS (to_tsvector('english', content)) STORED
  >>> );
  CREATE TABLE

  >>> INSERT INTO ts_example (content) VALUES ('the quick brown fox jumps over the lazy dog');
  INSERT 0 1

  >>> SELECT * FROM ts_example;
  id |                   content                   |                   search_vector_en
  ----+---------------------------------------------+-------------------------------------------------------
    1 | the quick brown fox jumps over the lazy dog | 'brown':3 'dog':9 'fox':4 'jump':5 'lazi':8 'quick':2
  (1 row)

  >>> INSERT INTO ts_example (content) VALUES ('the quick brown fox jumps over the lazy cat');
  INSERT 0 1

  >>> SELECT * FROM ts_example WHERE search_vector_en @@ to_tsquery('cat');
  id |                   content                   |                   search_vector_en
  ----+---------------------------------------------+-------------------------------------------------------
    2 | the quick brown fox jumps over the lazy cat | 'brown':3 'cat':9 'fox':4 'jump':5 'lazi':8 'quick':2
  (1 row)

  >>> SELECT * FROM ts_example WHERE search_vector_en @@ to_tsquery('dog');
  id |                   content                   |                   search_vector_en
  ----+---------------------------------------------+-------------------------------------------------------
    1 | the quick brown fox jumps over the lazy dog | 'brown':3 'dog':9 'fox':4 'jump':5 'lazi':8 'quick':2
  (1 row)

  >>> SELECT * FROM ts_example WHERE search_vector_en @@ to_tsquery('brown');
  id |                   content                   |                   search_vector_en
  ----+---------------------------------------------+-------------------------------------------------------
    1 | the quick brown fox jumps over the lazy dog | 'brown':3 'dog':9 'fox':4 'jump':5 'lazi':8 'quick':2
    2 | the quick brown fox jumps over the lazy cat | 'brown':3 'cat':9 'fox':4 'jump':5 'lazi':8 'quick':2
  (2 rows)

  >>> SELECT * FROM ts_example WHERE search_vector_en @@ to_tsquery('laziness');
  id |                   content                   |                   search_vector_en
  ----+---------------------------------------------+-------------------------------------------------------
    1 | the quick brown fox jumps over the lazy dog | 'brown':3 'dog':9 'fox':4 'jump':5 'lazi':8 'quick':2
    2 | the quick brown fox jumps over the lazy cat | 'brown':3 'cat':9 'fox':4 'jump':5 'lazi':8 'quick':2
  (2 rows)

	```
