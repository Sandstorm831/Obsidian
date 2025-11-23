Setting up the ssh-agent
- Initializing the ssh-agent
	```bash
	eval $(ssh-agent -s)
	```
- adding `ssh` key
	```bash
	ssh-add <PATH_TO_PRIVATE_KEY>
	# for example
	# ssh-add ~/.ssh/id_ed25519
	```
	