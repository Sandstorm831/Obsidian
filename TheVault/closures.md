- decorator is an example of a closure
- closure is defined by following things:
	- an `outer function`
	- `variables local to outer func`, these variables form the `enclosing scope` for the closure
	- `inner function`, a function inside the outer function
	- `returning inner function` object via outer function
	```python
	def outer_func(t1):
		a_var = "value"
		def inner_func(t2):
			print(a_var, "|", t2)
		return inner_func
	```
	- so this is a closure, as it has a  `inner_func`, `returning inner_func object`, `variables local to outer_func`, `outer_func`
	- ***Remember, all the variables local to `outer_func`, forms the enclosing scope of closure and is retained between multiple calls of the same `inner_func` obj***, 
	```python
	def make_counter():
	    count = 0
	    def counter():
	        nonlocal count
	        count += 1
	        return count
	    return counter
	
	a = make_couner()
	b = make_counter()
	a()
	# 1
	a()
	# 2
	b()
	# 1
	b()
	# 2
	a()
	# 3
	```