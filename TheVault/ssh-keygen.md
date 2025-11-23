Here are the command for generating `ssh-keys`
-  Initial command
	  ```bash
    ssh-keygen -t ed25519 -C "you_email@example.com"
	```
	- `-t`: type of key to generate
	- `-C`: comment, a human readable label, typically your email
- When prompted to **"Enter your file to save the key"**, you can press enter to accept the default file location or you can enter your own path. It's recommended to follow the default and if you need to change, keep the default path `~/.ssh/`
- 