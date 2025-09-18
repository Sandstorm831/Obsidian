- Primarily used for spawning new processes and running `shell commands`
	```python
import subprocess

subprocess.run(["python3", "-m", "src.main"], cwd="path/to/project")
	```
	`cwd`: is used to specify the location from which the given command had to be run