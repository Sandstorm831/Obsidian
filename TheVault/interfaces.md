- basic template to follow when trying to make an interface
```python
from abc import ABC, abstractmethod

class MyAbstractClass(ABC):

	@abstractmethod
	def myFunc1(self, param1, param2):
		pass
	
	@abstractmethod
	def myFunc2(self, param1, param2, param3):
		pass
	
```
