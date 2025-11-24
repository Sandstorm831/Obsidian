Here are the instructions and commands on how to add new users on linux
1. First the find the `login shell` of the current user by using the command
	```bash
	getent passwd "$USER"
	# output example
	# yourname:x:1000:1000::/home/yourname:/usr/bin/bash
	```
	Here, `/usr/bin/bash` is the login shell
2. We have to find all the groups current user is a part of by using the command
	```bash
	groups
	# example output
	# yourname docker wheel
	```
3. Now, we can create a user with name `yourname`, with groups `docker`and `wheel` and `/usr/bin/bash` as the `login shell` with the following command
	```bash
	sudo useradd -m -G wheel,docker -s /usr/bin/bash yourname
	```
	- `-m`: create a home directory
	- `-G` add the groups passed as a comma separated list of which user will be a part of
	- `-s`: specify `login shell`
4. To add password, follow the command
	```bash
	sudo passwd yourname
	```
5. To enable `sudo` access, follow the steps
	1. add user to `wheel` group
		```bash
		sudo usermod -aG wheel yourname
		```
		- `-a`: append user to `group` specified, work only when used with `-G`
		- `-G wheel`: specify the group ( `wheel` )
	2. open `sudoers` file
		```bash
		sudo visudo
		```
		always open the file using `visudo`, that is, use the above command only to open the `/etc/sudoers` file
	3. `uncomment` or `add` the following line
		```bash
		%wheel ALL=(ALL:ALL) ALL
		```
	4. Save and Exit: `Esc + :wq`
6. To check the user, and `sudo` access, follow
	1. login to the new user
		```bash
		su - yourname
		```
	2. test the following command
		```bash
		sudo echo "sudo works"
		```