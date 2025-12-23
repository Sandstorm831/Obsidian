> **DON'T USE IT**

- it is given that operations with money will be precise
- lc_monetary, this parameter stores the currency in which money will be stored
	Example
	```sql
	>>> SHOW lc_monetary;
	lc_monetary
	-------------
	en_IN
	(1 row)
	```

	-  currency is stored in Rupees right now, as sooon as we switch it
	```sql
	>>> SET lc_monetary='en_GB.UTF-8';
	```
	-  now th currency is stored in pounds
	- the problem is, if any table have Rupees initially, and suddenly somewhat changed this parameter to Pounds, now everything is pounds without any actual currency conversion.
	-  the values will remain same, 199.24 rupees will become 199.24 pounds. This is the problem
- it maintains precision only upto `2` decimal places and round-off anything after that to 2 decimal places
- for storing money, you should either use integer (`int2`,`int4`,` int8` whichever suits you), whereas you store them as lowest unit of that currency (also depending on your need)
	Example
	```sql
	-- Store 100.21 Rs as 10021, If you need more places after decimal, then store it with multiplying by 10^p, where p is the number of places after decimal you want to store
	```
- or you can store it as numeric and with defined precision and scale.
- for storing currency ( if there is a need ), you can use a separate column rather than storing them as money datatype