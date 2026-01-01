- There are 3 character types, 
	- fixed length `CHAR`
	- `CHARACTER VARYING`
	- `TEXT`
- All the three character types uses the same data-structure under the hood, so you fixed length `CHAR` doesn't give any performance benefit over `CHARACTER VARYING` and `TEXT`
- fixed length `CHAR`, might be the worst performing character-type among all the three character-types

- [[Postgres_Char]]
- [[Postgres_Character_Varying]]
- [[Postgres_Text]]

In order of preferences for use of character_types:
	`TEXT` > `CHARACTER VARYING`
don't use fixed length `CHAR`