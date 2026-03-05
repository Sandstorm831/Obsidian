- Bit strings are string of `0s` & `1s`
- It have 2 types `bit(n)` and `bit varying(n)` where `n` is positive integer. `bit(n)` type data must match the length `n` exactly and will throw error for longer or shorter strings. `bit varying(n)` data is of varying length up-to max length n whereas longer strings will be rejected
- It is completely `opaque`, and doesn't convey what the string mean. It can be useful in a few use cases (like storing `100s` of boolean values) but it won't be good choice in most.
	```sql
  >>> SELECT B'0011';
  ?column?
  ----------
  0011
  (1 row)

  >>> SELECT '0011'::BIT(4);
  bit
  ------
  0011
  (1 row)

  >>> SELECT pg_typeof('0011'::BIT(4));
  pg_typeof
  -----------
  bit
  (1 row)

  >>> CREATE TABLE bit_example(
  >>> bit3 bit(3),
  >>> bitv bit varying(32)
  >>> );
  CREATE TABLE

  >>> INSERT INTO bit_example (bit3, bitv) VALUES (B'001', B'010111110100111');
  INSERT 0 1
  >>> SELECT * FROM bit_example ;
  bit3 |      bitv
  ------+-----------------
  001  | 010111110100111
  (1 row)

  >>> INSERT INTO bit_example (bit3, bitv) VALUES (B'00', B'010111110100111');
  ERROR:  bit string length 2 does not match type bit(3)

  >>> INSERT INTO bit_example (bit3, bitv) VALUES (B'001', B'010111110100111010010101001010010100100100101000101001001001001010010010010100100100100101001001');
  ERROR:  bit string too long for type bit varying(32)
	```


- **NOTE : If one explicitly casts a bit-string value to `bit(n), it will be truncated or zero-padded on the right to be exactly `n` bits, without raising an error. Similarly, if one explicitly casts a bit-string value to `bit varying(n)`, it will be truncated on the right if it is more than `n` bits.**
	```sql
	>>> SELECT B'010111110100111010010101001010010100100100101000101001001001001010010010010100100100100101001001'::BIT VARYING(32);  
	             varbit                 
	----------------------------------  
	01011111010011101001010100101001  
	(1 row)  
  
  	>>> SELECT B'010111110100111010010101001010010100100100101000101001001001001010010010010100100100100101001001'::BIT(12);  
	    bit         
	--------------  
	010111110100  
	(1 row)
  
	>>> SELECT B'010111110100111010010101001010010100100100101000101001001001001010010010010100100100100101001001'::BIT(120);  
	                                                          bit                                                               
	--------------------------------------------------------------------------------------------------------------------------  
	010111110100111010010101001010010100100100101000101001001001001010010010010100100100100101001001000000000000000000000000  
	(1 row)  
	```

- You can store it as a integer also, but that's not helpful as it becomes more opaque. If you need to bit operations, it's better to store it in as bit string only
	```sql
  >>> SELECT B'0101'
  >>>     &  B'0001';  -- mask
  ?column?
  ----------
  0001
  (1 row)

  >>> SELECT B'0101'
  >>>      & B'0010';  -- mask
  ?column?
  ----------
  0000
  (1 row)

  -- AND operation between 2 bit strings
	```