If you want custom error handling the following case will suffice most of your cases
```python

class GlobalError(Exception):
	def __init__(self, message, context: dict = None):
		self.context = context or {}
		self.message = message
		super().__init__(message)
	
	def __str__(self):
		f_ctx = "\n".join("   {k}: {v}" for k, v in self.context.items())
		if f_ctx:
			return f"{self.message}\nContext:\n{f_ctx}"
		return self.message

class ParticError(GlobalError):
	pass

```
- `__str__`: will override the default `print(error)` and `str(error)` to the defined format
- `self.context`: this `dict` will contain all the necessary detail you want to pass on, here is a general case
	```python
	from errors import GlobalError
	
	try:
		raise TypeError()
	except Exception as e:
		context = {
			"cause": e,
			"module_name": __name__,
			"some_other_imp_pars": values
		}
		raise GlobalError(
			message = "some error occurred", 
			context = context
		) from e
	```
	the `from e` at last will make sure that this `exception` is due to above exception. python then preserves `e` as the **cause** and `GlobalError()` is what program actually sees