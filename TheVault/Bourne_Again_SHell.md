- To run a program in a detached window and redirect it's output to null
	```bash
<run_command_for program> > /dev/null 2>&1 &
	```
	-  `> /dev/null` -> This redirects the standard_output (`stdout`) to `/dev/null`, 
	- `/dev/null` is special location, that discards everything written to it
	- `2>&1` -> This redirects the standard_error (`stderr`) to wherever `stdout` is going, which is `/dev/null` in this case
	- `2` -> file descriptor for stderr
	- `1` -> file descriptor for stdout
	- `2>&1` -> send errors to the same place as standard_output
	- `&1` -> reference to the file descriptor 1
	- `&` -> tells the shell to run the command in the background

- In bash 
	- `0` -> `stdin` -> standard_input file descriptor
	- `1` -> `stdout` -> standard_output file descriptor
	- `2` -> `stderr` -> standard_error file descriptor

- Mount a external drive in the system
	```bash
$ lsblk -f # list all partitions and disk devices connected info
# find name and filesystem-type (fstype) of external drive
# you can identify yours by the label

$ mkdir /media/<mount_folder> # if you don't already have one

$ sudo mount -t <fstype> /dev/<name> /media/<mount_folder>
	```
	- `-t` -> tells the `file-system` type
	- `/dev/<name>` -> Linux represent hardware as files and device files live in `/dev/`
