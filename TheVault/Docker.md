- [Docker Compose](Docker_Compose)

- Enter a container
```bash
docker exec -it <container_name_or_id> bash
```

- Enter a Postgres container
```bash
docker exec -it <container_name_or_id> psql -U <POSTGRES_USER>
```

- Whenever you seem to find that `DNS` resolution is not working in docker compose or a container for some reason or there is some ***networking issue, try restarting the docker*** via the following command
	```bash
	sudo systemctl restart docker
	```
	It will solve the problem in most cases


- list container
```bash
docker ps
```

- list all containers (including stopped images)
```bash
docker ps -a
```

- stop a container
```bash
docker kill <container_name_or_id>
```

- remove a stopped container
```bash
docker rm <container_name_or_id>
```

- remove all the unused volumes : corresponding docker containers have been removed
```bash
docker volume prune

```

- List all docker images
```bash
docker image ls
```

- List all docker volumes
```bash
docker volume ls
```

- An example of a Dockerfile
```dockerfile
# It highlights a base image that will be pulled from dockerhub
FROM python:slim

# It sets the working directory to /app and navigate to it
WORKDIR /app

# Copy the requirements.txt file from current folder on
# local machine to docker image current folder (which is /app)
COPY requirements.txt .

# commands here are run while building the image
RUN pip install -r requirements.txt

# Copy everything from current directory to the
# current folder of image ignoring everything in
# .dockerignore file 
COPY . .

# CMD specifies the default command that will be run
# when the container is started from the built image
CMD [ "python3", "coddes.py" ]
```

- command to build the image
```bash
docker build -t <name_of_image>
```

