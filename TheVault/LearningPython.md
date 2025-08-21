Beginning of python learning
- [SQLite](SQLite)
- [Threads](Threading.md)
- [Decorator](Decorator)
- [PIL](Pillow)
- [DateTime](DateTime)
- [FastAPI](FastAPI)
- [IO](io)
- [Requests](requests)
- [Multiprocessing](Multiprocessing)
- [APScheduler](APScheduler)
- [Logging](Logging)
- [Errors](error_handling)

---
- `sort`
```python
# To sort a an array containing complex objects
# we can use key parameter, it takes a function
# which will be called on e[ach element before making
# comparison
a = [("rg", 23), ("rgd", 2), ("at", 24)]
a.sort(key=lambda elem: elem[1]) # sort the array by second elem of tuple
# >> a = [("rgd", 2), ("rg", 23), ("at",24)]
```

- Functionalities of `*`
	- `in func call` -> unpacks a `list/tuple` into separate arguments
	- `in func def` -> accepts any number of positional arguments
```python
def greet(*names):      # passing arbitrary number arguments
	def hello_func(a, b, c):
		print("hello ", a, ", ", b, ", ", c)
	hello_func(*names)

names = ["pi", "dy", "sy"]
greet(*names) # unpacking of names list

# hello  pi ,  dy ,  sy
```

- find the module name of a `func` you can use `__module__` attributes
	```python	
def a_func():
	print("the func")
func_module_name = a_func.__module__
	```
	other useful attributes:


| Type     | Attribute      | Description                                     |
| -------- | -------------- | ----------------------------------------------- |
| class    | `__name__`     | name with class is defined                      |
|          | `__module__`   | name of the module in which class is defined    |
|          | `__qualname__` | qualified name (includes the heirarchy/context) |
| function | `__name__`     | name with which function is defined             |
|          | `__qualname__` | qualified name (includes the heirarchy/context) |
|          | `__module__`   | module name in which function is defined        |

- in a python code, `__name__` gives the module of the file which is `__main__` if the file is run directly, and `module.submodule` if it's imported
	```python
def hello():
	print("hell with your hello")

if __name__ == '__main__':
	hello()
	```
	this code will print the `text` only when it is run directly without any imports, if we import the function, the `hello()` function call inside `if` block, will not run.