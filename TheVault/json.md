- loading a `json` file
```python
import json

with open("path/to/file.json", "r") as f:
	json_obj = json.load(f)

print(json_obj)
# {'PARKPOW_HOST': 'http://nginx:80'}
```
- updating `json_dict`
```python
updates = {
	"NO_HOST": "localhost",
	"PARKPOW_HOST": "no_host" # 
}

json_obj.update(updates)
print(json_obj)
# {'PARKPOW_HOST': 'no_host', "NO_HOST": "localhost"}
```
- saving the `json_dict` into a file
```python
with open("path/to/file.json", 'w') as f:
	json.dump(json_obj, f, indent=2)
```
- `json` file format
```json
{
  "key1": "value1",
  "key2": "value2"
}
```