- all of the containers are connected via internal network and can be reached out by their names as defined in `services` section `compose.yaml`file.
	```yaml
services:
	db:
		image: [IMAGE]
	
	my_app:
		image: [IMAGE_2]
	```
	so here my_app can connect to the db service via "db" as the host

- Whenever you seem to find that `DNS` resolution is not working in docker compose or a container for some reason or there is some ***networking issue, try restarting the docker*** via the following command
	```bash
	sudo systemctl restart docker
	```
	It will solve the problem in most cases

- example of `compose.yaml` file
```yaml
# services include all the images/services you 
# are going to run
services:
  # name of the service
  alpr:
    image: platerecognizer/alpr
	# environment variables
	environment:
	  - LICENSE_KEY=XXXXXX
	  - WORKERS=2
	
	# port mapping
	ports:
	  - 8080:8080
	
	# volume allocation
	volumes:
	  - license:/license
	
	# extra host name mapping
	extra_host:
	  - "host.docker.internal:host-gateway"
	  # here host.docker.internal is mapped to a host named
	  # host-gateway, and by-default in docker, host-gateway
	  # is mapped to the localhost or maching on which the
	  # docker container is running
	
	# mark dependency, the service listed here
	# are started before running current service
	depends_on:
	  - db
  db:
      image: postgres:16
      volumes:
        - postgres_data:/var/lib/postgresql/data/
      environment:
        - POSTGRES_DB=postgres
        - POSTGRES_USER=postgres
        - POSTGRES_PASSWORD=postgres

```
