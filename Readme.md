# Docker Swarm lab

Repository by docker swarm lab

## Contains:
* vagrant file to create:
  - 3 master
  - 1 node

* Compose with service to deploy


## Examples
* [DockerExamples](DockerExamples.md "DockerExamples.md")

* [DockerSwarm](DockerSwarm.md "DockerSwarm.md")




# create new user
$ curl -G http://192.168.77.21:8086/query --data-urlencode "q=CREATE USER root WITH PASSWORD 'root' WITH ALL PRIVILEGES"

# create database for cadvisor
$ curl -G http://192.168.77.21:8086/query -u root:root --data-urlencode "q=CREATE DATABASE cadvisor"
