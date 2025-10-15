It's basically responsible for the import operations.

- Here is an example of dynamically importing and executing the module
```python
	from importlib.util
	import os
	
	ENV = os.environ.get("ENVIRONMENT", "DEV").lower()
	settings_module_name = f"{ENV}_settings"
	settings_module = None
	try:
		settings_spec = importlib.util.find_spec(settings_module_name, package=__package__)
		# it finds the spec of the given module name, optionally 
		# relative to the specified package. Spec is a namespace
		# is containing the all the import-ralated information of
		# a module. __package__ variable contains the name of the 
		# current package.
		
		if settings_spec is None:
			raise ModuleNotFoundError("Text you want here")
		settings_module = importlib.util.module_from_spec(settings_spec)
		# Create a new module based on spec
		
		settings_spec_loader = settings_spec.loader
		if settings_spec_loader is None:
			raise ModuleNotFoundError("Text you want here")
		settings_spec_loader.exec_module(settings_module)
		# executes the module in it's own namespace
	except ModuleNotFoundError:
		raise
```