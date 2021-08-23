# Docker: Part 2

Running Multi-Container Apps via Docker

## Other Note(s)

- [Part 1](https://github.com/SSaquif/docker-notes/blob/master/part-1.md): Docker Fundamentals using a single react app as example
- [Part 3](https://github.com/SSaquif/docker-notes/blob/master/part-3.md): Covers Deployment

## Contents

<!-- toc -->

- [Docker: Part 2](#docker-part-2)
  - [Other Note(s)](#other-notes)
  - [Contents](#contents)
  - [Issues](#issues)
    - [Issue 1: File Permissions](#issue-1-file-permissions)
  - [Our Example App](#our-example-app)
  - [Advantage of Docker for Such Apps](#advantage-of-docker-for-such-apps)
  - [`yml` Files](#yml-files)
    - [`yml` vs `json`](#yml-vs-json)
    - [Which One to Pick?](#which-one-to-pick)
  - [Useful Refrences for `docker-compose.yml` Compose File](#useful-refrences-for-docker-composeyml-compose-file)
  - [`docker-compose.yml` File Breakdown](#docker-composeyml-file-breakdown)
    - [Example File](#example-file)
    - [version](#version)
    - [services](#services)
    - [ports](#ports)
    - [environment](#environment)
    - [volume](#volume)
    - [command](#command)
    - [Keywords Breakdown Table](#keywords-breakdown-table)
  - [Docker and Local MongoDB](#docker-and-local-mongodb)
    - [MongoDB Compass & Changing DB Port from 27017 to Something Else](#mongodb-compass--changing-db-port-from-27017-to-something-else)
  - [Docker & MongoDB Atlas](#docker--mongodb-atlas)
  - [Building Images](#building-images)
  - [Starting & Stopping the Application](#starting--stopping-the-application)
  - [Docker Network](#docker-network)
  - [Viewing Logs](#viewing-logs)
  - [Publishing Changes & Live Updates](#publishing-changes--live-updates)
  - [Database Migration](#database-migration)
    - [Some Basics](#some-basics)
    - [Running Migration Commands](#running-migration-commands)
    - [Waiting Issues](#waiting-issues)
    - [Solution](#solution)
    - [Final Improvement](#final-improvement)
  - [Running Tests](#running-tests)
  - [Quick Commands](#quick-commands)

<!-- tocstop -->

## Issues

### Issue 1: File Permissions

To get everything working I had to make some changes to the individual dockerfiles in the frontend and backend folders of the vidly app.

The changes had to do with file permissions, see comments in file below

```dockerfile
FROM node:14.16.0-alpine3.13

RUN addgroup app && adduser -S -G app app
# added next 2 lines
RUN mkdir /app
RUN chown -R app:app /app

USER app

WORKDIR /app
# added user+group permissions using --chown
COPY --chown=app:app package*.json ./
RUN npm install
# added user+group permissions using --chown
COPY --chown=app:app . .

EXPOSE 3001

CMD ["npm", "start"]
```

## Our Example App

The example app we will be suing is a fullstack MERN app:

`TODO & Side Note:` App is provided in mosh's course, will update it to one of my own apps later

> I will probably have to change the folder owners in the actual individual FE & BE dockerfile

1. Frontend React
2. Backend Node and Express
3. With a Mongo Database

## Advantage of Docker for Such Apps

Let's say we were not using doocker and we downloaded the app from sojme github repo. To get started we would have to do the following:

1. Run yarn install separately for the FE and BE
2. Setup our own MongoDB either locally or on cloud

Settingg up the db can be particularly time consuming task.

With `docker` we use `docker-compose` for such applications and if everything is setup correctly in the relevant docker setup files. We just pull the repo and run a single command to get the app running on any machine.

```bash
# Starting a Multi Container App
docker-compose up
```

## `yml` Files

docker-compose is an `yml` file.

`yml` and `yaml` are both valid extensios. They are the same.

### `yml` vs `json`

To understand `yml` better, it's best to compare with `json` files.

```json
{
  "name": "Greatest App Ever",
  "price": 149,
  "is_published": true,
  "tags": ["software", "devops"],
  "author": {
    "first_name": "Sadnan",
    "last_name": "Saquif"
  }
}
```

```yml
---
name: Greatest App Ever
price: 149
is_published: true
tags:
  - software
  - devops
author:
  first_name: Sadnan
  last_name: Saquif
```

| yml                                                                   | json                                                                 |
| --------------------------------------------------------------------- | -------------------------------------------------------------------- |
| File starts with ---                                                  | everything goes inside braces                                        |
| Indentation matters (idea comes from python)                          | Indentation doesn't matter                                           |
| comma not reuired at eol                                              | comma reuired at eol (except last property)                          |
| No need to double quotations "" (see exceptions for version)          | Strings go in double quotes `""`, no need of quotations for numbers  |
| However, that means no way to distinguish between strings and numbers | double quotes `""` allows to distinguish between strings and numbers |
| Arrays are represented as indented lists                              | Arrays are similar to js arrays                                      |
| Objects are represented with indented properties                      | Objects are similar to js objects                                    |

### Which One to Pick?

1. yml takes longer to parse so it's slower. This is due to not being able to distinguish between strings and numbers easily

2. `json` is more suitable for sharing data

3. `yml` is a bit easier to read. As hierarchial data is indented

4. This makes `yml` more suitable for configuration files

## Useful Refrences for `docker-compose.yml` Compose File

> `Important:` At the time of writing, version 3 of the compose file is the latest.

This Links are also refrenced below

- [Link to docs for v3](https://docs.docker.com/compose/compose-file/compose-file-v3/). Note that info about different properties used in the file can be found on the right hand side bar
- [Use variables form `.env` file](https://docs.docker.com/compose/env-file/)
- [Variable Substitution](https://docs.docker.com/compose/compose-file/compose-file-v3/#variable-substitution)

## `docker-compose.yml` File Breakdown

> All `docker-compose` commands needs to run from the folder where compose file is located

See folder `3-vidly` for actual example file

In this file we define the building block/services of our applications

This `docker-compose.yml` file does not seem it needs to start with ---

### Example File

```yml
version: "3.8"

services:
  frontend: # service name, can be anything see section below
    depends_on:
      - backend
    build: ./frontend # folder where the dockerfile is located
    ports: # mapping port of host:container
      - 3000:3000

  backend:
    depends_on:
      - db
    build: ./backend
    ports:
      - 3001:3001
    environment:
      DB_URL: mongodb://db/vidly # env variable, see variable sunbstitution link above as well
    command: ./docker-entrypoint.sh

  db:
    image: mongo:4.0-xenial # pulling image from dockerhub instead of bulding from dockerfile
    ports:
      - 27017:27017
    volumes:
      - vidly:/data/db

volumes:
  vidly: # No value, just property/volume name
```

### version

The version depends on which Docker Engine we are using. Check the [Compatibility Matrix](https://docs.docker.com/compose/compose-file/compose-versioning/)

Also this is the only property whose value needs to be in `""`

```yml
version: "3.8"
```

### services

This are basically the different parts of our app. We use the names `frontend, backend and db`. But the service names are arbitary. Another common convention is call frontend `web` and backend `api`

Each service should have it's own `dockerfile` unless it's something like a database. In which case we usually build using an image from dockerhub

> `Important`: Each service will have it's own dockerfile. Little trickier for services like DB. We pull an image for dockerhub for the DB. See the relevant Database sections below.

### ports

Port mappings for the application

Is a list since there can be multiple port mappings

Some ports are fixed, for example mongodb is `27017`, React is `3000`. Probably possible change port on container after mapping for all.

### environment

Environmental variables for the application. They can be a list or key value pair separated by `:` as in the example above

Sometimes we might want to use variables from a `.env` file instead. See [Variable Substitution](https://docs.docker.com/compose/compose-file/compose-file-v3/#variable-substitution)

The `DB_URL` in this case is the mongodb connection string

### volume

Basically volumes for persisting data. Read more about volumes in `part-1.md`.

For why it's being use here with the database and for a better understanding of usage in general see section [Docker and Local MongoDB](#docker-and-local-mongodb)

### command

This will ovewrite CMD/ENTRYPOINT command of the dockerfile

To know more about what it does in this particular example see section [Database Migration](#database-migration)

### Keywords Breakdown Table

For more info on the keywords see [official docs](https://docs.docker.com/compose/compose-file/compose-file-v3/), see right hand side bar to navigate keywords.

| Keywords | Description                                                                         |
| -------- | ----------------------------------------------------------------------------------- |
| version  | [see above](https://github.com/SSaquif/docker-notes/blob/master/part-2.md#version)  |
| services | [see above](https://github.com/SSaquif/docker-notes/blob/master/part-2.md#services) |
| ports    | used to map port of host to container                                               |
| build    | path to folder of where the docker file is located                                  |
| volume   | [see above](https://github.com/SSaquif/docker-notes/blob/master/part-2.md#volume)   |

## Docker and Local MongoDB

`TODO` Put his section in the mongo db notes as well

Also see section [Migrating the Database](https://github.com/SSaquif/docker-notes/blob/master/part-2.md#migrating-the-database)

When not using mongo in cloud (atlas), there are 3 things we have to do.

1. `Step 1:` Put the mongo connection string as an env var in the `docker-compose.yml` file for the `backend/api` service .
2. The env var `DB_URL: mongodb://db/vidly` has the following signature
   - mongodb://<host-name>/<db-name>
   - the `<host-name>` is the same as the service name of the databse in the example file it's `db`
   - `<db-name>` is self explanatory, here it's `vidly`
3. `Step 2:` In the `db` service add a `volume`
   - because we DO NOT want mongodb to write data to the temporary file system of the container
   - and we have seen earlier volume are the way we persist data in docker
4. The volume in the example is `vidly:/data/db`, has following format
   - `<volume-name>:/<directory-inside-container>`
   - `<volume-name>` can be anything, here it's `vidly` to be consistent with app name
   - `<directory-inside-container>` is directory of the container where the data that needs to persist is stored
   - for `mongodb` that directory is by default `/data/db`
   - By mapping that directory to a volume, that data is actually stored outside the container
5. Finally we have to define another property called `volume` at the same level as `services` & `version`
   - Inside it we define a property whose name is the volume name we used, here it's vidly
   - the property has `NO VALUE`

### MongoDB Compass & Changing DB Port from 27017 to Something Else

Finally if everything works, you can connect to the database in the container through mongodb compass by giving it the right port number. If you use default port `27017` you can just click connect. This will probably not work if you already have a mongo process running in that port. Click on `Fill in connection fields individually` just above the connection string form, if you need to change the port number to something else.

## Docker & MongoDB Atlas

Need to figure this out, will probably have use [variables from `.env files`](https://docs.docker.com/compose/env-file/)

`TODO` Put his section in the mongo db notes as well

## Building Images

I had to make some changes regarding file permission for builds to be successful on my computer. See Issue 1 at the top of the file for setails

```bash
# build image
docker-compose build
# man page
docker-compose build --help
# build without using cache
docker-compose build --no-cache
# attempt to pull newer version of image if applicable
docker-compose build --pull
```

## Starting & Stopping the Application

```bash
# Start
docker-compose up
# (re)-build and start
docker-compose up --build
# detached mode, start containers in background
docker-compose up -d

# check containers related to the build
docker-compose ps
# You get the following, State == Up == You are good to go
# Each container has a number (here it's 1)
# Because we can start multiple containers from same image for high availability & scalability
       Name                     Command               State                      Ports
----------------------------------------------------------------------------------------------------------
3-vidly_backend_1    docker-entrypoint.sh ./doc ...   Up      0.0.0.0:3001->3001/tcp,:::3001->3001/tcp
3-vidly_db_1         docker-entrypoint.sh mongod      Up      0.0.0.0:27017->27017/tcp,:::27017->27017/tcp
3-vidly_frontend_1   docker-entrypoint.sh npm start   Up      0.0.0.0:3000->3000/tcp,:::3000->3000/tcp

# stop application
docker-compose down
```

## Docker Network

[See video](https://codewithmosh.com/courses/the-ultimate-docker-course/lectures/31450213)

When you run docker-compose up, you will probably see something like this. So it creates a docker network.

```bash
docker-compose up-d
Creating network "3-vidly_default" with the default driver
Creating 3-vidly_db_1 ... done
Creating 3-vidly_backend_1 ... done
Creating 3-vidly_frontend_1 ... done
```

> At this point the video shows how to ping the backend container from frontend container. It also talks about how docker comes with a embedded `dns server` and containers have `dns resolvers`. Resolvers in containers ask the server for ip addresses of other containers in the network to communicate.This is how the containers form a network. Video has nice diagrams and all, also shows the network adapters of containers. So just watch the video.

## Viewing Logs

```bash
# view logs of all relevant containers
docker-compose logs
# view logs of individual containers
# no need for compose, so like before
# -f tag is for follow (optional)
docker logs <container> -f
```

## Publishing Changes & Live Updates

I have a feeling, in this part I might face the same issue as in part-1 issue-2. Skipped for now. Will come back to it when working on actual projects

## Database Migration

### Some Basics

> Most of the time when we release our app, we want our database to be in a particular shape with some data.
>
> This is called Database Migration

For Node and MongoDB we can use the npm package [migrate-mongo](https://www.npmjs.com/package/migrate-mongo) as our migration tool, other dev stacks/frameworks have similar packages or library.

Using such migration tool, we can create DB migration scripts. In the vidly project the migration scripts are stored in the `/migrations` folder. The script is pretty simple, there are 2 functions. One to upgrade the db and one to downgrade it. The up function inserts a bunch of movies to the movies collection and the down function removes them.

The script is called populate-movies and is timestamped as can be seen from the name.

### Running Migration Commands

So now we can run migration commands in the terminal as folows (see migrate-mongo npm link for more details)

```bash
migrate-mongo up
```

There's also and alias for the above command in the scripts section of our `package.json` as `db:up`, so alternatively we can run

```bash
npm run db:up
# this should also work (not tested)
npm db:up
```

### Waiting Issues

Say we want to do DB Migration as part of starting our application.

We add the `command` property to our `backend` service in our `docker-compose.yml` file

However, this can cause some issues. As it is possible that our db engine is not ready at this point (even though the db container is running) and we need to wait for that to be setup before we can execute the `migrate-mongo` command.

```yaml
# ovewrites CMD of the dockerfile
command: migrate-mongo && npm start # Waiting Issues
```

### Solution

Since starting the db engine can take several seconds, we need to use an waiting script. The [docker docs](https://docs.docker.com/compose/startup-order/) (or google docker wait for container) has some recommendations.

We are using the [sh-compatible wait-for](https://github.com/Eficode/wait-for) script. We simply put he `shell script` file in the root of `backend` folder and update the command as follows. We want to wait for port `27017` of our `db` container.

The `wait-for` script takes 1 argument in the following format:`<service-name>:<port-number>`

```yaml
# Solves waiting issues
# ./wait-for <service-name>:<port-number>
command: ./wait-for db:27017 && migrate-mongo && npm start
```

### Final Improvement

Since the command is a bit long, we can simply turn it into a shell script of it's own. In the project folder the script is named `docker-entrypoint.sh` (I beleive this is a docker naming convention). The script is pretty straightforward as follows

```sh
#!/bin/sh

echo "Waiting for MongoDB to start..."
./wait-for db:27017

echo "Migrating the databse..."
npm run db:up

echo "Starting the server..."
npm start
```

Finally we update the `docker-compose.yml` file one last time as follows

```yml
command: ./docker-entrypoint.sh # extension .sh is optional I believe
```

## Running Tests

## Quick Commands

```bash
# All docker-compose commands needs to run from the folder where compose file is located

# build image
docker-compose build
# man page
docker-compose build --help
# build without using cache
docker-compose build --no-cache
# attempt to pull newer version of image if applicable
docker-compose build --pull

# Starting a Multi Container App
docker-compose up
# (re)-build and start
docker-compose up --build
# detached mode, start containers in background
docker-compose up -d

# check containers related to the build
docker-compose ps

# stop application
docker-compose down

# view logs of all relevant containers
docker-compose logs
# view logs of individual containers
# no need for compose, so like before
# -f tag is for follow (optional)
docker logs <container> -f
```
