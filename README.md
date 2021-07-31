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
  - [Quick Commands](#quick-commands)

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

## Quick Commands

I have to use `sudo` before running docker each time

See sections above for details on some commands

```bash
# build image named first-dockerized-app from current folder
docker build -t first-dockerized-app .

# list all docker images and details
docker image ls
# or
docker images

# get help about image command
docker image

# run app from any directory using image name, after image creation
docker run image-name

# pulling image from dockerhub
docker pull image-id
```
