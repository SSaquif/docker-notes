# Docker: Part 2

Running Multi-Container Apps via Docker and Deployment to Digital Ocean

## Other Section(s)

- [Part 1](https://github.com/SSaquif/docker-notes/blob/master/part-1.md): Docker Fundamentals using a single react app as example

## Contents

<!-- toc -->

- [Docker: Part 2](#docker-part-2)
  - [Other Section(s)](#other-sections)
  - [Contents](#contents)
  - [Our Example App](#our-example-app)
  - [Advantage of Docker for Such Apps](#advantage-of-docker-for-such-apps)
  - [Quick comands](#quick-comands)

<!-- tocstop -->

## Our Example App

The example app we will be suing is a fullstack MERN app:

`TODO & Side Note:` App is provide in the course, will update it to my one of own apps later

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

## Quick comands

```bash
# Starting a Multi Container App
docker-compose up
```