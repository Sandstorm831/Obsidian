1. `INET`
	- it holds an `IPv4` or `IPv6` addresses and optionally it's `subnet` all in one field
		```sql
	  >>> CREATE TABLE inet_examples(
      >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
      >>> ip_addresses INET
      >>> );
      CREATE TABLE

      >>> INSERT INTO inet_examples (ip_addresses)
      >>> VALUES
      >>> ('192.168.1.10/24'),                           -- Host address with subnet mask
      >>> ('10.0.0.1'),                                  -- Host address without subnet
      >>> ('::1/128'),                                   -- IPv6 loopback address
      >>> ('2001:db8::/32'),                             -- IPv6 network
      >>> ('2001:db8:85a3:8d3:1319:8a2e:370:7348');      -- IPv6 host address
      INSERT 0 5
      >>>
      >>> SELECT * from inet_examples;
      id |             ip_addresses
      ----+--------------------------------------
        1 | 192.168.1.10/24
        2 | 10.0.0.1
        3 | ::1
        4 | 2001:db8::/32
        5 | 2001:db8:85a3:8d3:1319:8a2e:370:7348
      (5 rows)
		```

	- It is much more efficient and compact as compared to text column
		```sql
      >>> 	SELECT pg_column_size('2001:db8:85a3:8d3:1319:8a2e:370:7348'::text), pg_column_size('2001:db8:85a3:8d3:1319:8a2e:370:7348'::INET);
	  pg_column_size | pg_column_size
	 ----------------+----------------
	              40 |             22
	  (1 row)
		```

	- Here are some network functions that comes handy
	```sql
      >>> 	SELECT ip_addresses,
      >>> 	host(ip_addresses) as host_only,
      >>> 	masklen(ip_addresses) as mask_length,
      >>> 	network(ip_addresses) as network_only,
      >>> 	abbrev(ip_addresses) as abbreviated_ip
      >>> 	FROM inet_examples;
	               ip_addresses             |              host_only               | mask_length |               network_only               |            abbreviated_ip
	  --------------------------------------+--------------------------------------+-------------+------------------------------------------+--------------------------------------
	   192.168.1.10/24                      | 192.168.1.10                         |          24 | 192.168.1.0/24                           | 192.168.1.10/24
	   10.0.0.1                             | 10.0.0.1                             |          32 | 10.0.0.1/32                              | 10.0.0.1
	   ::1                                  | ::1                                  |         128 | ::1/128                                  | ::1
	   2001:db8::/32                        | 2001:db8::                           |          32 | 2001:db8::/32                            | 2001:db8::/32
	   2001:db8:85a3:8d3:1319:8a2e:370:7348 | 2001:db8:85a3:8d3:1319:8a2e:370:7348 |         128 | 2001:db8:85a3:8d3:1319:8a2e:370:7348/128 | 2001:db8:85a3:8d3:1319:8a2e:370:7348
	   (5 rows)
	```


2. `CIDR`
	- holds an `IPv4` and `IPv6` network specification.
	- format of input is `address/y`, where `address` is an `IPv4` or `IPv6` representation of network's lowest address and `y` is number of `bits` in `netmask`
	- if `y` is omitted, it is automatically added and will be at least large enough to hold all the octets written in input.
	- `error` will be thrown when network address has bits set to the right of specified `netmask`
		```sql
      >>> CREATE TABLE cidr_example(
      >>> id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
      >>> network_address cidr
      >>> );
      CREATE TABLE

      >>> INSERT INTO cidr_example (network_address) VALUES
      >>> ('192.168.24.0/24'),          -- Network with IPv4
      >>> ('10/8'),                     -- Larger IPv4 Network
      >>> ('2001:db8::/48');            -- IPv6 network
      INSERT 0 3

      >>> SELECT * FROM cidr_example;
      id | network_address
      ----+-----------------
        1 | 192.168.24.0/24
        2 | 10.0.0.0/8
        3 | 2001:db8::/48
      (3 rows)

      >>> SELECT '192.168.0.1/24'::cidr;
      ERROR:  invalid cidr value: "192.168.0.1/24"
      LINE 1: SELECT '192.168.0.1/24'::cidr;
                ^
      DETAIL:  Value has bits set to right of mask.
		```

3. MAC Addresses
	- It has 2 formats, `6-byte` and `8-byte`. Some `MAC-address` requires `8 bytes` which can be stored in `macaddr8` while `6 byte` `mac addresses` can be `type-casted` to `macaddr8` also.
		```sql
	  >>> SELECT ('08:00:2b:01:02:03'::macaddr);
	        macaddr
	  -------------------
	  08:00:2b:01:02:03
	  (1 row)

      >>> SELECT ('08:00:2b:01:02:03'::macaddr8);
	          macaddr8
	  -------------------------
	  08:00:2b:ff:fe:01:02:03
	  (1 row)

      >>> SELECT ('08:00:2b:01:02:03:04:05'::macaddr8);
	          macaddr8
	  -------------------------
	  08:00:2b:01:02:03:04:05
	  (1 row)
		```