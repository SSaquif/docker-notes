# Docker

All notes on Docker

## Contents

<!-- toc -->

- [Docker](#docker)
  - [Contents](#contents)
  - [Installation](#installation)
  - [Introduction](#introduction)
    - [Why Docker?](#why-docker)
  - [Containers vs Virtual Machines](#containers-vs-virtual-machines)
  - [Docker Architecture](#docker-architecture)
  - [Dockerization of an App](#dockerization-of-an-app)
    - [Dockerfile Example](#dockerfile-example)
    - [Running The App using Docker](#running-the-app-using-docker)
  - [A Note for Windows Users](#a-note-for-windows-users)
  - [Containers vs Images](#containers-vs-images)
  - [DockerFile Instructions](#dockerfile-instructions)
    - [From](#from)
    - [WORKDIR](#workdir)
    - [COPY](#copy)
    - [ADD](#add)
    - [RUN](#run)
    - [ENV](#env)
    - [Excluding Files .dockerignore](#excluding-files-dockerignore)
    - [Managing Users](#managing-users)
  - [Building Images](#building-images)
    - [Registries](#registries)
    - [Selecting the Right Base Image](#selecting-the-right-base-image)
    - [Running Shell Sessions Inside Containers](#running-shell-sessions-inside-containers)
    - [React App Image](#react-app-image)
  - [Quick Commands](#quick-commands)
  - [References](#references)

<!-- tocstop -->

## Installation

Use goolge for instructions.

## Introduction

### Why Docker?

Often things will run in one machine but not another, why?

    1. Missing file(s)
    2. Software/Dependency Version Mismatches

With `Docker` we can package up our entire Applications (with all the correct version for each dependency) and run it on any machine that has docker.

So with new jobs you don't have to spend a day setting up the environment.

We can use `docker` to set everything up as required and `docker` will create an isolated enviroment as required called a `container`

## Containers vs Virtual Machines

|                                                      Containers                                                      |                                    Virtual Machines                                    |
| :------------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------: |
|                                          An isolated env for running an app                                          |                    An abstraction of a machine (physical hardware)                     |
|                                                   Are lightweight                                                    |       Uses a type of Software called a Hypervisors used to create and manage VMS       |
|                                                                                                                      |                         Ex of Hypervisor: Virtual Box, VMware                          |
|                                                                                                                      |      The host machine can then use the hypervisor, to create VM(s) within itself       |
|                                                 Uses OS of the host                                                  |                             Each VM needs a full blown OS                              |
|                                                    Starts quickly                                                    |                                Therefore slow to start                                 |
| Less resource intensive and no allocation of hardware resources needed. A single host can run hundreds of containers | Resource intensive as hardware resources of the host have to be allocated between VMs. |

## Docker Architecture

// TODO
Write Notes for section using

1. Class slides
2. Mosh's video

Quick Summmary for now

1. Uses client server architecture
2. Use Rest API for client-server communication
3. Server is the docker engine
4. There's a docker desktop app for mac and windows but not linux yet.

## Dockerization of an App

We can add docker to any application by simply adding a `Dockerfile`, the name is capitalised

Docker uses this file to package up our app into an `image`

The image contains everything our app needs to run, ex

    1. A cut-down OS
    2. A runtime env (Node/Python etc)
    3. Application files
    4. 3rd party libraries
    5. Environmental Variables

### Dockerfile Example

```dockerfile
FROM node:alpine
COPY . /app
WORKDIR /app
CMD node index.js
```

1. Line 1: `FROM node:alpine`

   - Typically we start with a base image (it can be OS, RE like node)
   - Base Image will have bunch of files and we will add to it
   - We started of with `node` image
   - We then optionally added the `linux distribution alphine`
   - `alpine` is a very small distribution so it will keep image size small

2. Line 2: `COPY . /app`

   - We want our image to have all our required files
   - The Dockerfile is at the root of our project, ie `.`
   - So we copy over everything and put in a `/app` folder for the image to use

3. Line 3: `WORKDIR /app`

   - This line is optional
   - This basically specifies where the image will find the files
   - ie the working directory for the image to use

4. Line 4: `CMD node index.js `
   - Finally this line tells docker how to run the app
   - If we omitted line 3, we would have to do `CMD node /app/index.js `

### Running The App using Docker

First We tell docker to build/package up our application.

Then we simply

```bash
# -t means tag, a tag name for our image
# In this case first-dockerized-app
# Then specify where to find dockerfile
# In this case the current directory so just `.`
docker build -t first-dockerized-app .

# run app from any directory using image name
docker run first-dockerized-app
```

## A Note for Windows Users

Windows 10+ ships with a Linux kernel. So when using Docker, you can choose between Windows or Linux containers. Windows containers (processes) need to talk to the Windows kernel under the hood. Linux containers need the Linux kernel.

These are essentially two different isolated worlds. You can choose between these two worlds by right-clicking on the Docker icon in the notification tray (in the bottom status bar).

Remember: the images and containers in the Windows world are invisible to the Linux world. You'd need Windows containers only if you need an image that starts from Windows.

## Containers vs Images

Just rmember `multiple Containers` can be spinned up from the `same Image`. But the `Containers` are all `isolated`.

|                                         Images                                         |                                  Containers                                  |
| :------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------: |
| A image contains all the files and configuration settings needed to run an application |   Once we have an image we can start one or multople container(s) from it    |
|                                     A cut down OS                                      |                 Is can of like in VM in the following sense                  |
|                                  3rd Party Libraries                                   |            1. Provides an isolated environment for executing apps            |
|                                   Application files                                    |                  2. Can be stoppeds and restarted like VMs                   |
|                                 Environment Variables                                  | Is just a sytem process, that has it's own file system provided by the image |
|                                                                                        | Each container is an `isolated instance`, can't access each others resources |
|                                                                                        |          There are ways to share data between containers if needed           |

## DockerFile Instructions

Following section looks into how to build docker images by going through each docker command. In the next `Building Images section` we will see an example for building an image of the boilerplate react app.

The section outlines

- Basic instructions like adding and removing files for docker
- running terminal commands on docker
- setting `env variables` in docker
- exposing ports

### From

### WORKDIR

Creates the working directory for the image

```docker
WORKDIR /app
```

### COPY

To copy files to the image. The structure is basically `COPY what where`

`.` signifies the `WORKDIR`

We can only copy from the directory the Dockerfile is in. We can not copy anything outside of it, ie no goin uo folder(s)

The `COPY` command has 2 forms, one is like regular command. The other form takes an array like this `COPY ["filename", "destination"]`

```dockerfile
# copy one file into WORKDIR /app
COPY package.json /app
# copy multiple files, but where copy needs to end with /
COPY package.json package-lock.json /app/
# copy using pattern
COPY package*.json /app/
# copy everything from current dir into WORKDIR
# absolute path
COPY . /app/
# relative path if we aldready set `WORKDIR /app`
COPY . .
# copy file(s) with space in name, have to use the the array form
COPY ["file 1.txt", "readme.md", "."]
```

### ADD

Same as `COPY`, but has `2 additional features.`

First, You can copy using a url if you have access to that file

```docker
ADD https://www.somewhere.com/data.json .
```

Second, If you add a zip file, it will be automatically unzipped

```docker
ADD somefile.zip .
```

### RUN

Can run any valid OS commands using this.

```docker
RUN ls -l
RUN npm install
```

### ENV

Used to set up environmental variables.

```docker
# new better way
ENV API_URL=http://www.somesite.com/

# old way, space instead of =
ENV API_URL http://www.somesite.com/
```

### Excluding Files .dockerignore

We use `.dockerignore` to exlude files from the image. The file name is `case-sensitive`

Basically docker's `.gitignore`, whatever you don't want copied put it here. Example `node_modules`. As usual transferring image after copying nude_modules to a remote docker server is a waste. The image size will be huge.

### Managing Users

Also see managings users in linux notes if needed. In order to first create user.

```bash
# the image used in the example was ubuntu
# interactively run bash in a pseudo shell
# login as the user john
docker exec -it -u john imageid bash
```

## Building Images

We will now combine the knowledge from previous section and build and example image

### Registries

Images are kept in registries. The default registry is dockerhub. But some images, like microsoft images, are kept in other registries like the microsoft container registry. For those you need to use the complete URL. Example:

```docker
# mcr == microsoft container registry
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build-env
```

### Selecting the Right Base Image

The base image can be an OS or OS + Runtime Environment

If you are a js dev you likely want want to start with node image, for C# you want to start with .NET image, for python a pythom image and so on.

[Language specific samples](https://docs.docker.com/language/)

For our example we need a node image, there are many flavors of node image + some OS available to us. They are identified by there tags. Each tag has the OS and CPU architecture specified and also the size. We use alpine OS here due to it's much smaller image size compared to something like ubuntu.

[Node Images](https://hub.docker.com/_/node?tab=tags&page=1&ordering=last_updated)

By default the latest tag is used (see below). But we will never do this because let's say the latest node version is updated. Then the next time we build our app using docker, it will update the node version. Something we might not want

```docker
# both commands are identical
# don't do this
FROM node
FROM node:latest
```

### Running Shell Sessions Inside Containers

Often we want to Run Shell Sessions Inside Containers. To have a look around.

We can do this as follows

```bash
# Run Shell Sessions Inside Containers
docker run -it first-dockerized-app sh # alpine
docker run -it first-dockerized-app bash # ubuntu
```

### React App Image

## Quick Commands

I have to use `sudo` before running docker each time

See sections above for details on some commands

```bash
# switches
# -i = interactive, brings up an interactive shell
# for ex if we run just a node image, -i will bring up the node cli

# -t = means to allocate a pseudo-tty (pseudo terminal, see linux notes)
# or google pseudo-tty

# build/rebuild image named first-dockerized-app from current folder
docker build -t first-dockerized-app .

# Run Shell Sessions Inside Containers
docker run -it first-dockerized-app sh # alpine
docker run -it first-dockerized-app bash # ubuntu

# get help about image command
docker image

# list all docker images and details
docker image ls
# or
docker images


# run app from any directory using image name, after image creation
docker run image-name

# pulling image from dockerhub
docker pull image-id

# running an image/app
# If we don't have the image locally
# It will try and automatically pull one with same id, if that exists
docker run image-id

# start container in interactive mode using -i
# -t switch might be unnecessary
# according to man docker run
# -t means to allocate a pseudo-tty (pseudo terminal, see linux notes)
# ubuntu is the image-id
# this will run an ubuntu image
# a new ubuntu shell will show up
dock run -it ubuntu

# list of running processes/containers
docker ps

# list all containers (stopped ones too)
docker ps -a

```

## References

1. [Mosh Hamedani's Docker Course](https://codewithmosh.com/courses/the-ultimate-docker-course/lectures/31448372)
2. Prof Shane McIntosh's COMP 437: Software Delivery Course Slides
3. [Language specific samples](https://docs.docker.com/language/)
