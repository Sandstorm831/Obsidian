The beginning of everything.

- [Python](LearningPython)
- [AuthJS](AuthJS)
- [Docker](Docker)
- [SQLite](SQLite)
- [PostgreSQL](PostgreSQL)
- [Bash](Bourne_Again_SHell)
- [Computer Science Fundamentals](CS_Fundamentals)
- [Music](Music)
- [Linux](linux)
- [SMTP_Server](smtp_server)
- [to_learn](toLearn)
- [[environement_variables]]
- [[Authentication_and_Authorization]]
- [[Github]]
- [[Curl]]
- [[Git]]

- `base64` is used to turn binary data into `ASCII` characters, it uses
```
ABCDEFGHIJKLMNOPQRSTUVWXYZ
abcdefghijklmnopqrstuvwxyz
0123456789
+/
```
	it also use = as padding at the end
- `base64` is not `url` safe due to characters `+` & `/` and padding `=`, thus `base64url` changes are made such that
```
+ -> -
/ -> _
=(padding) -> removed
```