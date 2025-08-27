- primarily used to define a new `datatype` in form of a class, here's some code that will suffice most of your need
```python
from dataclasses import dataclass, asdict
from typing import Optional
from enum import Enum

class MyEnum(Enum):
	name_1: 1
	name_2: 2
	name_3: 3

@dataclass
class MyDataType:
	name1: str
	name2: int
	name3: Optional[str] = None   # marks this field as optional, default None
	name4: int = MyEnum.name_1.value
	# this is preferred format for using enums, by
	# setting it's type to that of original and using
	# originally defined enum's value
	# here, it's used to give a default value

my_var = MyDataType(
	name1="name1",
	name2=2,
	name4=MyEnum.name_3.value
)
my_var_dict = asdict(my_var)
```
