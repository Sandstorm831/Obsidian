Decorator is a simple function that is used when you want to do a thing before or/and after the a function.

```python
#### decorator.py 
import functools
def decor(func):
	@functools.wraps(func)
	def wrapper(*args, **kwargs):
		# do what you want to do before
		print("done before the function")
		result = func(*args, **kwargs)
		# do whatevery you want to do after
		print("done after the function")
		return result
	return wrapper
####

#### main.py
from decorator import decor
# To use the decorator you can use it like
@decor
def printer(name: str = "erlang"):
	print(f"Hi, my name is {name}")

# @decor is just a syntactical sugar
# it basically boils down to this
# printer = decor(printer)
```

### Parameterised decorator 
Now if you want to pass a parameter to a decorator, you can't do that directly, but since functions (so decorators too) are first class citizens (they can treated as normal objects and passed around like that), we can wrap the decorator around another function that takes those parameters and return the decorator
```python
#### decorator.py 
import functools
def decor(param = DEFAULT_VALUE):
	def inner(func):
		@functools.wraps(func)
		def wrapper(*args, **kwargs):
			# do what you want to do before
			print("done before the function")
			result = func(*args, **kwargs)
			# do whatevery you want to do after
			print("done after the function")
			return result
		return wrapper
	return inner
####

#### main.py
from decorator import decor
# To use the decorator
@decor(param="hello")
def printer(name: str = "erlang"):
	print(f"Hi, my name is {name}")
```

- [Multiprocessing_decorator](Multiprocessing)