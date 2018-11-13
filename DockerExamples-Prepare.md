# Preparación de los Ejemplos Docker

En esta página se encuentra la documentación para preparar las instancias de virtualbox y vagrant necesarias para el seguimiento de los ejercicios.


## Levantar las instancias de vagrant
``` [bash]
  cd vagrant
  vagrant up
```

Una vez que se disponga de las instancias corriendo es posible acceder vía ssh:
``` [bash]
  vagrant ssh (swarm-master* | swarm-node*)
```

Con el fichero inicial se generan 4 máquinas virtuales:
  * swarm-master1
  * swarm-master2
  * swarm-master3
  * swarm-node1

## Instalación de Docker

``` [bash]
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce
```

## Arrancar el demonio de Docker y gestión de permisos a un usuario

Inicialmente con el usuario por defecto no se tiene permisos para ejecutar docker sin hacer "sudo"
``` [bash]
sudo systemctl start docker
docker ps -a
  Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.39/containers/json?all=1: dial unix /var/run/docker.sock: connect: permission denied
```
Se añade el grupo docker a cualquier usuario que necesite gestión sin necesidad de hacer "sudo"
``` [bash]
sudo usermod -G docker vagrant
```
Para que el cambio tenga efecto, es necesario salir del terminal y volver a entrar.
``` [bash]
logout
  Connection to 127.0.0.1 closed.

vagrant ssh swarm-master1
docker ps -a
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
