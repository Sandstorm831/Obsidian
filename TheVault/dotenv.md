- Read the `.env` variables 
```python
# pip install python-dotenv
from dotenv import load_dotenv
import os
load_dotenv()

env_var = os.getenv("ENV_VAR_NAME")
```

- add or update a `.env` variables
```python
import dotenv

key_value = "val"

dotenv.set_key("path_to_env_file", "key_name", key_value)
```
