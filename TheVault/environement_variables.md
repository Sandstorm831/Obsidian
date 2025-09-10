- store of key-value pairs
- Every process has it's own environment
- Each process inherits OS and User level environment variables that are exported during the start-up process
- `shell_variables` which are defined in a shell-session are not available to the child process as environment variables unless exported.
```bash
$ MX_NAME=Sandstorm
$ echo $MX_NAME
Sandstorm
$ python3
>>> import os
>>> print(os.getenv("HOME"))
'/home/xyz'
>>> print(os.getenv("MX_NAME"))
None

```
- `shell_variables` which are exported are available to all the children of that shell session only, any shell session outside will not recognise that `shell` variable
```bash
$ export NX_NAME=sandstorm
$ echo $NX_NAME
sandstorm
$ python3
>>> import os
>>> print(os.getenv("NX_NAME"))
sandstorm
```