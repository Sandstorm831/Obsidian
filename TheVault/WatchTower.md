A docker container that checks for updates for all the docker containers running and pull the updated images, stops the containers and restart the containers with new pulled images.

- In a docker compose `yaml` file, the basic service config is as follows
	```yaml
services:
  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
	```
	this container will watch other containers and check for update every 24 hours.

- If you have some private repository from which you want to pull images in which you have to provide login credentials, in that case, following additional steps need to be followed
	- create a `config.json` file in the following format
		```json
	{
		"auths": {
			"<REGISTRY_NAME>": {
				"auth": "XXXXXX"
			}
		}
	}
		```
		The required string should be generates as follows
		`echo -n 'username:password' | base64`

	- pass the `config.json` to the docker container using vol as this
	```yaml
	services:
	  watchtower:
	    image: containrrr/watchtower
	    volumes:
	      - /var/run/docker.sock:/var/run/docker.sock
	      - <PATH>/config.json:/config.json
	```


- To reduce the wait time from 24 hours to any-other value, here is an example that sets the check time to `30 seconds`
```yaml
services:
  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --interval 30
```