# Docker with React with Nginx Workshop

## Preparation

```bash
git clone https://github.com/opsta/skooldio-docker-20240305.git workshop
cd ~/workshop/src/react-nginx/
```

## Create Dockerfile for React with Nginx

* `touch ~/workshop/src/react-nginx/Dockerfile`

```Dockerfile
# syntax=docker/dockerfile:1.4

# 1. For build React app
FROM node:20.11.1-alpine3.19 AS development

# Set working directory
WORKDIR /app

# Copy dependency files
COPY package.json /app/package.json
COPY package-lock.json /app/package-lock.json

# Same as npm install
RUN npm ci

COPY . /app

ENV CI=true
ENV PORT=3000

CMD [ "npm", "start" ]

FROM development AS build

RUN npm run build


FROM development as dev-envs
RUN <<EOF
apt-get update
apt-get install -y --no-install-recommends git
EOF

RUN <<EOF
useradd -s /bin/bash -m vscode
groupadd docker
usermod -aG docker vscode
EOF
# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /
CMD [ "npm", "start" ]

# 2. For Nginx setup
FROM nginx:alpine

# Copy config nginx
COPY --from=build /app/.nginx/nginx.conf /etc/nginx/conf.d/default.conf

WORKDIR /usr/share/nginx/html

# Remove default nginx static assets
RUN rm -rf ./*

# Copy static assets from builder stage
COPY --from=build /app/build .

# Containers run nginx with global directives and daemon off
ENTRYPOINT ["nginx", "-g", "daemon off;"]
```

* Run the following commands to build and run React with Nginx container

```bash
# Build Docker Image name react-nginx
docker build -t react-nginx .
# See newly build Docker Image
docker images
# Run react with nginx
docker run -d --name react-nginx -p 8080:8080 react-nginx
# See running containers
docker ps -a
# Try Web Preview with / as path
```

## Navigation

* Previous: [Docker Command Line Workshop](01-docker-cli.md)
* [Home](../README.md)
* Next: [Docker Compose](03-docker-compose.md)
