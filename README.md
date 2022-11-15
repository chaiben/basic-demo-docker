# 010. Docker

## Docker Basic ([website](https://www.docker.com))

- List containers + stoped containers

  `docker ps -a`

- Remove all containers

  `docker container rm -f $(docker container ls -aq)`

- List docker images

  `docker image ls`

- Remove only one image

  `docker image rm IMAGE_ID`

- Remove all images

  `docker image rm -f $(docker image ls -q)`

- Access docker container shell

  `docker exec -it <mycontainer> bash`

  `docker exec -it <mycontainer> sh`

- Access docker container shell with ROOT user

  `docker exec -it -u root <mycontainer> sh`

  `docker exec -it -u root <mycontainer> bash`

## Docker Compose

- Start the application

  `docker compose up`

- Run at the background

  `docker compose up -d`

- Run from a different compose file

  `docker compose -f FILE_PATH up`

- Stop application

  `docker compose down`

## Compose Samples

#### Wordpress

```yaml
version: "3.3"

services:
  db:
    image: mariadb:10.5.8
    volumes:
      - db_data:/var/lib/mysql
    ports:
      - "33060:3306"
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - $HOME/src/bodevi_web:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
```

#### MongoDB

```yaml
# Use root/example as user/password credentials
version: "3.1"

services:
  mongo:
    image: mongo
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: example
    ports:
      - 27017:27017

  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8081:8081
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: example
      ME_CONFIG_MONGODB_URL: mongodb://root:example@mongo:27017/
```

#### Frontend + Backend + Database

This case, you need to create a project with 2 folders

- frontend
- backend

Each of them need to have a “Dockerfiler”

```yaml
version: "3.8"
services:
  web:
    build: ./frontend
    ports:
      - 3000:3000
  api:
    build: ./backend
    ports:
      - 3001:3001
    environment:
      - DB_URL=mongodb://db/DATABASE
  db:
    image: mongo:4.0-xenial
    ports:
      - 27017:27017
    volumes:
      - db_vol:/data/db
volumes:
  db_vol:
```

## Dockerfile

#### Node project

```typescript
FROM node:19-alpine3.15

RUN addgroup app && adduser -S -G app app
USER app

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

EXPOSE 3001

CMD ["npm", "start"]
```

- Create a build image

  `docker build -t [USERNAME]/[APPNAME]:[VERSION] .`

  `docker build -t chaiben/demoapp:1.0 .`

- Run

  `docker run -p 3001:3001 [IMAGEID]`

- Run with a volume

  `docker run --mount source=shared-stuff, target=/stuff`

## More details

[Learn Docker in 7 Easy Steps - Full Beginner's Tutorial](https://www.youtube.com/watch?v=gAkwW2tuIqE)

[GitHub - fireship-io/docker-nodejs-basic-demo: A simple demo running Node.js on Docker](https://github.com/fireship-io/docker-nodejs-basic-demo)

[Docker Compose Tutorial](https://www.youtube.com/watch?v=HG6yIjZapSA)

[Presentaciones de Google: inicio de sesión](https://docs.google.com/presentation/d/1O64Ec0uLEJiNhhBrtfDgsqU3K04oV9ebhNgsFZwVYgg/edit#slide=id.p)
