# Ejemplos Docker Swarm

En esta segunda parte vamos a definir que es un servicio, un stack y un cluster swarm de contenedores.

### Prerequisitos
Para poder gestionar los servicios en un cluster de contenedores es necesario disponer de docker-compose instalado.
La documentación oficial se encuentra en https://docs.docker.com/compose/install/

``` [bash]
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  100   617    0   617    0     0   1493      0 --:--:-- --:--:-- --:--:--  1497
  100 11.1M  100 11.1M    0     0  3864k      0  0:00:02  0:00:02 --:--:-- 5196k

$ sudo chmod +x /usr/local/bin/docker-compose
$ docker-compose --version
  docker-compose version 1.23.1, build b02f1306

```
Es importante ver que versión queremos instalar y disponemos del changelog en https://github.com/docker/compose/releases

## Parte 1: Docker Compose

[DockerSwarmCompose Parte1](DockerSwarmCompose.md "DockerSwarmCompose.md")

## Parte 2: Docker Swarm

[DockerSwarm Parte2](DockerSwarmParte2.md "DockerSwarmParte2.md")

## Parte 3: Docker Swarm

[DockerSwarm Parte3](DockerSwarmParte3.md "DockerSwarmParte3.md")
