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

- You can share files between 2 containers by sharing the same volume between them
- To restart a service of a docker compose running setup
```bash
docker compose restart <name_of_service>
```
- `hostname`: declares a custom host name for the service container to use

- `environment variables` passed through docker compose takes precedence over other process `environment variables` with the same name, as they are passed first to the process, but, if `overwriting` is allowed than docker-compose `environment variables` can be overwritten, you can read more [here](environement_variables)

- To run a command in a service container, you can use
```bash
docker compose exec <service_name> <command>
```

- To pass in secrets to docker-compose without hard-coding them, you can use two methods
	- create a `.env` file in the same folder as of `docker-compose.yml` and have the following structure.
		```env
		########## .env #############
		POSTGRES_USER=myuser
		POSTGRES_PASSWORD=secret
		
		######### docker-compose.yml ##########3
		services:
		  db:
		    image: postgres
		    environment:
		      - POSTGRES_USER=${POSTGRES_USER}
		      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
		```
	- create a external `env` file and pass the example: `secrets.env` file, in the same folder as of `docker-compose.yml` and have the following structure.
		```env
		########## secrets.env #############
		POSTGRES_USER=myuser
		POSTGRES_PASSWORD=secret
		
		######### docker-compose.yml ##########3
		services:
		  db:
		    image: postgres
		    env_file:
		      - secrets.env
		    environment:
		      - POSTGRES_USER=${POSTGRES_USER}
		      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
