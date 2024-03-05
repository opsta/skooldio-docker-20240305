# Docker Compose Workshop

## Prerequisite

* Install `docker compose` command

## Change Docker Command to Docker Compose

* `touch ~/workshop/src/react-nginx/docker-compose.yml`

```yaml
services:
  react-dev:
    build:
      context: .
      target: development
    container_name: react-dev
    ports:
      - "8080:3000"
    volumes:
      - .:/app
```

* Run Container with Docker Compose command

```bash
# Delete current running container first
docker rm -f react-nginx react-dev

docker compose up
```

* Update `README.md` by changing how to run rating service with Docker Compose instead

````markdown
## How to run with Docker Compose

```bash
docker compose up
```
````

* Commit and push code

## Docker Compose Utility Commands

```bash
# To only build docker image
docker compose build
# To force build Docker Image everytime
docker compose up --build
# To choose custom Docker Compose file
docker compose -f custom-compose.yml up
# To run Docker Compose in background
docker compose up -d
# To show status of containers
docker compose ps
# To stop all containers after docker compose up -d
docker compose stop
# To start all containers after docker compose stop
docker compose start
# To restart all containers
docker compose restart
# To show stdout from all containers
docker compose logs
docker compose logs -f
# To show all containers processes
docker compose top
# To list all images in Docker Compose file
docker compose images
# To stop and clean all docker compose up resources
docker compose down
```

## Navigation

* Previous: [Docker with React Workshop](02-docker-react.md)
* [Home](../README.md)
