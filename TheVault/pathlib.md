- some of the important ways to use `Path`
```python
from pathlib import Path

file_location = Path(__file__).resolve()
# resolve() does the work resolving `..` components and symlinks
# symlinks (similar to shortcuts in windows)

file_dir = file_location.parent

# recursively go the successive parents
for parent in file_location.parents:
	print(parent)

# output
# /home/rgd/Documents/alpr/src
# /home/rgd/Documents/alpr
# /home/rgd/Documents
# /home/rgd
# /home
# /

str_path = str(Path(__file__).resolve().parent)
# converts the paths to string
```