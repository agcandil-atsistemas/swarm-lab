# Ejemplos Docker Swarm

## Parte 1: Docker Compose

---

#### Introducción
Compose es una herramienta para definir y ejecutar aplicaciones *multi-container*. Con compose se usa una fichero YAML para configurar los servicios de la propia aplicación. Posteriormente, te permite, con un único comando, crear e iniciar todos estos servicios desde el fichero de definición.

Compose te permite trabajar en todos los entornos, Producción, Staging (uat), Desarrollo, testing,... facilitando los flujos de CI/CD.

El uso de Compose se resume en tres pasos:
* Define tu entorno de aplicación en los Dockerfile de cada contenedor, que puedan ser reproducibles en cualquier lugar.
* Define los servicios en un docker-compose.yml, que facilite la ejecución de todos conjuntamente en un entorno aislado.
* Ejecuta el docker-compose y se arrancará la aplicación completa.

#### Definición de un fichero Compose
Un fichero docker-compose.yaml es de la siguiente manera:
``` [bash]
version: '3'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```
Toda la información de la gramática se encuentra en: https://docs.docker.com/compose/compose-file/

#### Ejecutando nuestro primer compose

Como primer paso, vamos parar todos los contenedores que esten arrancados
``` [bash]
$ docker ps -a
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                   PORTS                  NAMES
  9dd5bc6e4f1a        my-nginx            "nginx -g 'daemon of…"   28 minutes ago      Up 28 minutes            0.0.0.0:9081->80/tcp   my-nginx
  b1cf2a0a002a        nginx               "nginx -g 'daemon of…"   2 hours ago         Up 2 hours               0.0.0.0:9080->80/tcp   some-nginx
  0a581579fff6        nginx               "nginx -g 'daemon of…"   3 hours ago         Up 3 hours               80/tcp                 relaxed_darwin
  9728096fe67c        nginx               "nginx -g 'daemon of…"   3 hours ago         Exited (0) 3 hours ago                          goofy_mayer
  d515982d2264        nginx               "nginx -g 'daemon of…"   3 hours ago         Exited (0) 3 hours ago                          lucid_mirzakhani
  96f4ba5b5657        hello-world         "/hello"                 8 hours ago         Exited (0) 8 hours ago                          naughty_lehmann
$ docker container stop 9dd5bc6e4f1a b1cf2a0a002a 0a581579fff6
  9dd5bc6e4f1a
  b1cf2a0a002a
  0a581579fff6

```

Para ver las opciones que ofrece docker-compose podemos acceder a la ayuda general o propia del comando a ejecutar.

``` [bash]
$ docker-compose --help
  Define and run multi-container applications with Docker.

  Usage:
    docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
    docker-compose -h|--help

  Options:
    -f, --file FILE             Specify an alternate compose file
                                (default: docker-compose.yml)
    -p, --project-name NAME     Specify an alternate project name
                                (default: directory name)
    --verbose                   Show more output
    --log-level LEVEL           Set log level (DEBUG, INFO, WARNING, ERROR, CRITICAL)
    --no-ansi                   Do not print ANSI control characters
    -v, --version               Print version and exit
    -H, --host HOST             Daemon socket to connect to

    --tls                       Use TLS; implied by --tlsverify
    --tlscacert CA_PATH         Trust certs signed only by this CA
    --tlscert CLIENT_CERT_PATH  Path to TLS certificate file
    --tlskey TLS_KEY_PATH       Path to TLS key file
    --tlsverify                 Use TLS and verify the remote
    --skip-hostname-check       Don't check the daemon's hostname against the
                                name specified in the client certificate
    --project-directory PATH    Specify an alternate working directory
                                (default: the path of the Compose file)
    --compatibility             If set, Compose will attempt to convert deploy
                                keys in v3 files to their non-Swarm equivalent

  Commands:
    build              Build or rebuild services
    bundle             Generate a Docker bundle from the Compose file
    config             Validate and view the Compose file
    create             Create services
    down               Stop and remove containers, networks, images, and volumes
    events             Receive real time events from containers
    exec               Execute a command in a running container
    help               Get help on a command
    images             List images
    kill               Kill containers
    logs               View output from containers
    pause              Pause services
    port               Print the public port for a port binding
    ps                 List containers
    pull               Pull service images
    push               Push service images
    restart            Restart services
    rm                 Remove stopped containers
    run                Run a one-off command
    scale              Set number of containers for a service
    start              Start services
    stop               Stop services
    top                Display the running processes
    unpause            Unpause services
    up                 Create and start containers
    version            Show the Docker-Compose version information


$ docker-compose up --help
  Builds, (re)creates, starts, and attaches to containers for a service.

  Unless they are already running, this command also starts any linked services.

  The `docker-compose up` command aggregates the output of each container. When
  the command exits, all containers are stopped. Running `docker-compose up -d`
  starts the containers in the background and leaves them running.

  If there are existing containers for a service, and the service's configuration
  or image was changed after the container's creation, `docker-compose up` picks
  up the changes by stopping and recreating the containers (preserving mounted
  volumes). To prevent Compose from picking up changes, use the `--no-recreate`
  flag.

  If you want to force Compose to stop and recreate all containers, use the
  `--force-recreate` flag.

  Usage: up [options] [--scale SERVICE=NUM...] [SERVICE...]

  Options:
      -d, --detach               Detached mode: Run containers in the background,
                                 print new container names. Incompatible with
                                 --abort-on-container-exit.
      --no-color                 Produce monochrome output.
      --quiet-pull               Pull without printing progress information
      --no-deps                  Don't start linked services.
      --force-recreate           Recreate containers even if their configuration
                                 and image haven't changed.
      --always-recreate-deps     Recreate dependent containers.
                                 Incompatible with --no-recreate.
      --no-recreate              If containers already exist, don't recreate
                                 them. Incompatible with --force-recreate and -V.
      --no-build                 Don't build an image, even if it's missing.
      --no-start                 Don't start the services after creating them.
      --build                    Build images before starting containers.
      --abort-on-container-exit  Stops all containers if any container was
                                 stopped. Incompatible with -d.
      -t, --timeout TIMEOUT      Use this timeout in seconds for container
                                 shutdown when attached or when containers are
                                 already running. (default: 10)
      -V, --renew-anon-volumes   Recreate anonymous volumes instead of retrieving
                                 data from the previous containers.
      --remove-orphans           Remove containers for services not defined
                                 in the Compose file.
      --exit-code-from SERVICE   Return the exit code of the selected service
                                 container. Implies --abort-on-container-exit.
      --scale SERVICE=NUM        Scale SERVICE to NUM instances. Overrides the
                                 `scale` setting in the Compose file if present.
```

Se genear el docker-compose.yml con la configuración de nginx desplegado en el capitulo anterior. El ejemplo se encuentra en [docker-compose.yml](docker-compose/docker-compose.yml "docker-compose example")

``` [bash]
$ mkdir docker-compose
$ vi docker-compose/docker-compose.yml
$ cd docker-compose/
$ docker-compose up
  WARNING: Some services (nginx) use the 'deploy' key, which will be ignored. Compose does not support 'deploy' configuration - use `docker stack deploy` to deploy to a swarm.
  Creating network "docker-compose_devops" with the default driver
  Creating volume "docker-compose_nginx" with default driver
  Creating docker-compose_nginx_1_dee16d817e31 ... done
  Attaching to docker-compose_nginx_1_3eb64d3de1db
  ^CGracefully stopping... (press Ctrl+C again to force)
  Stopping docker-compose_nginx_1_3eb64d3de1db ... done

$ docker ps -a        
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS               NAMES
  0f19ba2eb008        nginx               "nginx -g 'daemon of…"   26 seconds ago      Exited (0) 7 seconds ago                           docker-compose_nginx_1_3eb64d3de1db
  9dd5bc6e4f1a        my-nginx            "nginx -g 'daemon of…"   2 hours ago         Exited (0) About an hour ago                       my-nginx
  b1cf2a0a002a        nginx               "nginx -g 'daemon of…"   4 hours ago         Exited (0) About an hour ago                       some-nginx
  0a581579fff6        nginx               "nginx -g 'daemon of…"   4 hours ago         Exited (0) About an hour ago                       relaxed_darwin
  9728096fe67c        nginx               "nginx -g 'daemon of…"   4 hours ago         Exited (0) 4 hours ago                             goofy_mayer
  d515982d2264        nginx               "nginx -g 'daemon of…"   4 hours ago         Exited (0) 4 hours ago                             lucid_mirzakhani
  96f4ba5b5657        hello-world         "/hello"                 10 hours ago        Exited (0) 10 hours ago                            naughty_lehmann

$ docker-compose up -d
  WARNING: Some services (nginx) use the 'deploy' key, which will be ignored. Compose does not support 'deploy' configuration - use `docker stack deploy` to deploy to a swarm.
  Starting docker-compose_nginx_1_3eb64d3de1db ... done

```
Es importante destacar que volver a ejecutar el mismo compose no genera un nuevo contenedor, si no cambia la configuración que obligue a regenerarlo, arrancando el mismo que en la primera ejecución:

``` [bash]
$ docker ps -a
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS                  NAMES
  0f19ba2eb008        nginx               "nginx -g 'daemon of…"   2 minutes ago       Up 22 seconds                  0.0.0.0:9080->80/tcp   docker-compose_nginx_1_3eb64d3de1db
  9dd5bc6e4f1a        my-nginx            "nginx -g 'daemon of…"   2 hours ago         Exited (0) About an hour ago                          my-nginx
  b1cf2a0a002a        nginx               "nginx -g 'daemon of…"   4 hours ago         Exited (0) About an hour ago                          some-nginx
  0a581579fff6        nginx               "nginx -g 'daemon of…"   4 hours ago         Exited (0) About an hour ago                          relaxed_darwin
  9728096fe67c        nginx               "nginx -g 'daemon of…"   4 hours ago         Exited (0) 4 hours ago                                goofy_mayer
  d515982d2264        nginx               "nginx -g 'daemon of…"   5 hours ago         Exited (0) 4 hours ago                                lucid_mirzakhani
  96f4ba5b5657        hello-world         "/hello"                 10 hours ago        Exited (0) 10 hours ago                               naughty_lehmann

$ curl localhost:9080/helloworld.html
  <!DOCTYPE html PUBLIC "-//IETF//DTD HTML 2.0//EN">
  <HTML>
     <HEAD>
        <TITLE>
           A Small Hello
        </TITLE>
     </HEAD>
  <BODY>
     <H1>Hi</H1>
     <P>This is very minimal "hello world" HTML document.</P>
  </BODY>
  </HTML>
``` 
