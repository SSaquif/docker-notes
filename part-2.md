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
  - [`yml` Files](#yml-files)
    - [`yml` vs `json`](#yml-vs-json)
    - [Which One to Pick?](#which-one-to-pick)
  - [Quick Comands](#quick-comands)

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

| yml                                                                   | json                                                 |
| --------------------------------------------------------------------- | ---------------------------------------------------- |
| File starts with ---                                                  | everything goes inside braces                        |
| Indentation matters (idea comes from python)                          | Indentation doesn't matter                           |
| comma not reuired at eol                                              | comma reuired at eol (except last property)          |
| No need to double quotations ""                                       | Strings go in "", no need of quotations for numbers  |
| However, that means no way to distinguish between strings and numbers | "" allows to distinguish between strings and numbers |
| Arrays are represented as indented lists                              | Arrays are similar to js arrays                      |
| Objects are represented with indented properties                      | Objects are similar to js objects                    |

### Which One to Pick?

1. yml takes longer to parse so it's slower. This is due to not being able to distinguish between strings and numbers easily

2. `json` is more suitable for sharing data

3. `yml` is a bit easier to read. As hierarchial data is indented

4. This makes `yml` more suitable for configuration files

## Quick Comands

```bash
# Starting a Multi Container App
docker-compose up
```
