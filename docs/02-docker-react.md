# Docker with React with Nginx Workshop

## Preparation

```bash
git clone https://github.com/opsta/skooldio-docker-20240305.git workshop
cd ~/workshop/src/react-nginx/
```

## Create Dockerfile for React for Development

* `touch ~/workshop/src/react-nginx/Dockerfile`

```Dockerfile
# syntax=docker/dockerfile:1.6.0

# 1. For build React app
FROM node:20.11.1-alpine3.19 AS development

# Set working directory
WORKDIR /app

# Copy dependency files
COPY package.json /app/package.json
COPY package-lock.json /app/package-lock.json

# ci = clean-install
# Same as npm install
RUN npm ci

COPY . /app

ENV CI=true
ENV PORT=3000

CMD [ "npm", "start" ]
```

* Run the following command to build Docker Image and run React app

```bash
# Build Docker Image name react-nginx
docker build -t react-dev .
# See newly build Docker Image
docker images
# Run react with nginx
docker run -d --name react-dev -v $(pwd):/app -p 8080:3000 react-dev
# See running containers
docker ps -a
# Try Web Preview on port 8080 with / as path
```

* Try to change line 14 in `~/workshop/src/react-nginx/src/App.js` to `https://www.opsta.co.th`
* Refresh web preview in browser to see the change

## Create Dockerfile for React to run in production with Nginx

* Append the following content to `Dockerfile`

```Dockerfile
# 2.1 For production build
FROM development AS build

# npm run build to create an optimized production build
RUN npm run build


# 2.2 For Nginx setup as result
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
docker run -d --name react-nginx -p 8081:80 react-nginx
# See running containers
docker ps -a
# Try Web Preview onport 8081
```

## Navigation

* Previous: [Docker Command Line Workshop](01-docker-cli.md)
* [Home](../README.md)
* Next: [Docker Compose](03-docker-compose.md)
