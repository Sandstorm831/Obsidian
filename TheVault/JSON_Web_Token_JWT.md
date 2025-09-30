JWT can be think of as a compact and secure way of transmitting info in JSON

----
### Structure
It contains 3 parts separated by `.` and each part have a separate function
- Header
- Payload
- Signature
All of these are `base64` encoded strings, a JWT generally looks like
```text
xxxx.yyyyy.zzzz
```
In non-signed JWTs, the last part can be absent so the final token can look like
```text
xxxx.yyyyy.
```

- ##### Header
	Primarily, it'll contain 2 key-value pairs. 
	- `typ`: type of token, which is always "JWT"
	- `alg`: cryptography algorithm used for signing, such as, `HMAC SHA256` or `RSA`
	Example:
	```json
	{
		"alg": "HS256",
		"typ": "JWT"
	}
	```
	


- ##### Payload
	It's a set of claims that needs to be transmitted. There are 3 types of claims.
	- `Registered claim names
		- `iss`: Issuer, who issued JWT
		- `sub`: Subject, subject of JWT
		- `aud`: Audience, Intended recipient
		- `exp`: Expiration Time, time after which claim should not be accepted
		- `nbf`: Not before, time before which claim should not accepted
		- `iat`: Issued at, timestamp of JWT
		- `jit`: `JWT` Id
	- `Public claim names`
	- `Private claim names`
	Example:
	```json
	{
		"iss": "joe",
		"exp": 1300819380,
		"admin": True
	}
	```

- ##### Signature
	It takes the following to sign the JWT
	- Header
	- Payload
	- a Secret
	- Signing Algorithm
	Example: using `HMAC SHA256` as Signing Algorithm
	```text
	HMACSHA256(
		base64UrlEncode(Header) + "." + base64UrlEncode(Payload),
		Secret
	)
	```
