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