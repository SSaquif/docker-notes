# Docker

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

We can add docker to any application by simply adding a `dockerfile`

Docker uses this file to package up our app into an `image`

The image contains everything our app needs to run, ex

    1. A cut-down OS
    2. A runtime env (Node/Python etc)
    3. Application files
    4. 3rd party libraries
    5. Environmental Variables
