# Ejemplos Docker

## Parte 3: Gestión de imágenes y contenedores

---

#### Trabajar con un servicio de nginx
Para entender bien como funciona las imagenes y contenedores docker, vamos a trabajar con una imagen de un servicio real, para ello se usará nginx.
https://hub.docker.com/_/nginx/

#### Descarga de una imagen
Con docker se puede descargar una imagen sin necesidad de arrancar un contenedor con la misma.

``` [bash]
$ docker pull nginx
  Using default tag: latest
  latest: Pulling from library/nginx
  f17d81b4b692: Pull complete
  82dca86e04c3: Pull complete
  046ccb106982: Pull complete
  Digest: sha256:d59a1aa7866258751a261bae525a1842c7ff0662d4f34a355d5f36826abc0341
  Status: Downloaded newer image for nginx:latest
$ docker image ls   
  REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
  nginx               latest              62f816a209e6        6 days ago          109MB
  hello-world         latest              4ab4c602aa5e        2 months ago        1.84kB
$ docker container ls -a
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                   PORTS               NAMES
  96f4ba5b5657        hello-world         "/hello"            5 hours ago         Exited (0) 5 hours ago                       naughty_lehmann

```

#### Arrancar un contenedor de una imagen
Al arrancar un contenedor por defecto, la línea de comandos muestra la salida estandar (standard output) del contenedor.
Para salir del mismo es necesario recuperar el control (Ctrl+c), lo que provoca la salida del propio contenedor.

``` [bash]
$ docker run nginx

^C$ docker container ls -a
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
  d515982d2264        nginx               "nginx -g 'daemon of…"   2 minutes ago       Exited (0) 3 seconds ago                       lucid_mirzakhani
  96f4ba5b5657        hello-world         "/hello"                 5 hours ago         Exited (0) 5 hours ago                         naughty_lehmann

```

#### Servicios en ejecución
En este punto hay que identificar porqué el primer contenedor que se ejecutó arrancaba y paraba, y el de nginx se quedaba en ejecución. Para entenderlo, aunque se verá en un punto posterior, vamos a ver una línea del Dockerfile, la que ejecuta el servicio del propio contenedor.

* hello-world:
https://github.com/docker-library/hello-world/tree/b715c35271f1d18832480bde75fe17b93db26414/amd64/hello-world

      CMD ["/hello"]


* nginx
https://github.com/nginxinc/docker-nginx/blob/f4d30145c60c433966df96f618d78513fee9d322/stable/alpine/Dockerfile

        CMD ["nginx", "-g", "daemon off;"]


#### Ver detalles de una imagen
``` [bash]
$ docker image inspect nginx
[
    {
        "Id": "sha256:62f816a209e6b57dd5fe98c1994fe3ab19ba4e1fee2a5ec6d77f303be4ed90e9",
        "RepoTags": [
            "nginx:latest"
        ],
        "RepoDigests": [
            "nginx@sha256:d59a1aa7866258751a261bae525a1842c7ff0662d4f34a355d5f36826abc0341"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2018-11-07T00:21:43.991532421Z",
        "Container": "29e34d70f3cd6465a605282d7d50f72e9a3a90ff806d576025b0396f9c9f032b",
        "ContainerConfig": {
            "Hostname": "29e34d70f3cd",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.15.6-1~stretch",
                "NJS_VERSION=1.15.6.0.2.5-1~stretch"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"nginx\" \"-g\" \"daemon off;\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:c4f4f735431ad7725e5517c9ecbc712d4af9957e4809b18512ca28bf3500def7",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": [],
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGTERM"
        },
        "DockerVersion": "17.06.2-ce",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.15.6-1~stretch",
                "NJS_VERSION=1.15.6.0.2.5-1~stretch"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:c4f4f735431ad7725e5517c9ecbc712d4af9957e4809b18512ca28bf3500def7",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": [],
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGTERM"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 109109054,
        "VirtualSize": 109109054,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/e11f08e576620d59641c307473eeccce35910ea5beef6982405ad80d88844bd7/diff:/var/lib/docker/overlay2/6847f3019cb4199591af75e7fe7e15ddc704e576004de1185e5b5e7c43c19e94/diff",
                "MergedDir": "/var/lib/docker/overlay2/14697c77066ebd5bdedc6da022d04ba6c009e29c8a7949b2cb2d3083ff7de870/merged",
                "UpperDir": "/var/lib/docker/overlay2/14697c77066ebd5bdedc6da022d04ba6c009e29c8a7949b2cb2d3083ff7de870/diff",
                "WorkDir": "/var/lib/docker/overlay2/14697c77066ebd5bdedc6da022d04ba6c009e29c8a7949b2cb2d3083ff7de870/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:237472299760d6726d376385edd9e79c310fe91d794bc9870d038417d448c2d5",
                "sha256:6ccbee34dd1052580674858262f7f29e9c1be9f0eda5bfdf79b6045faf030af7",
                "sha256:ad9ac0e6043bccdcbac25fdbfe849c643e2cff5f28be6468603b16a3c805ecdd"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]

```

#### Arrancar el contenedor en modo desatendido.
Para arrancar un contenedor en modo desatendido es necesario añadir el modificado -d

``` [bash]
$ docker run -d nginx
  0a581579fff616bb65159634cd434376aded65aa4108d01c71b87637b093a4ea
$ docker container ls   
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
  0a581579fff6        nginx               "nginx -g 'daemon of…"   16 seconds ago      Up 15 seconds       80/tcp              relaxed_darwin

$ docker logs relaxed_darwin

$ curl localhost
  curl: (7) Failed connect to localhost:80; Connection refused

$ docker run --help

  Usage:	docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

  Run a command in a new container

  Options:
        --add-host list                  Add a custom host-to-IP mapping (host:ip)
    -a, --attach list                    Attach to STDIN, STDOUT or STDERR
        --blkio-weight uint16            Block IO (relative weight), between 10 and 1000, or 0
                                         to disable (default 0)
        --blkio-weight-device list       Block IO weight (relative device weight) (default [])
        --cap-add list                   Add Linux capabilities
        --cap-drop list                  Drop Linux capabilities
        --cgroup-parent string           Optional parent cgroup for the container
        --cidfile string                 Write the container ID to the file
        --cpu-period int                 Limit CPU CFS (Completely Fair Scheduler) period
        --cpu-quota int                  Limit CPU CFS (Completely Fair Scheduler) quota
        --cpu-rt-period int              Limit CPU real-time period in microseconds
        --cpu-rt-runtime int             Limit CPU real-time runtime in microseconds
    -c, --cpu-shares int                 CPU shares (relative weight)
        --cpus decimal                   Number of CPUs
        --cpuset-cpus string             CPUs in which to allow execution (0-3, 0,1)
        --cpuset-mems string             MEMs in which to allow execution (0-3, 0,1)
    -d, --detach                         Run container in background and print container ID
        --detach-keys string             Override the key sequence for detaching a container
        --device list                    Add a host device to the container
        --device-cgroup-rule list        Add a rule to the cgroup allowed devices list
        --device-read-bps list           Limit read rate (bytes per second) from a device
                                         (default [])
        --device-read-iops list          Limit read rate (IO per second) from a device (default [])
        --device-write-bps list          Limit write rate (bytes per second) to a device (default [])
        --device-write-iops list         Limit write rate (IO per second) to a device (default [])
        --disable-content-trust          Skip image verification (default true)
        --dns list                       Set custom DNS servers
        --dns-option list                Set DNS options
        --dns-search list                Set custom DNS search domains
        --entrypoint string              Overwrite the default ENTRYPOINT of the image
    -e, --env list                       Set environment variables
        --env-file list                  Read in a file of environment variables
        --expose list                    Expose a port or a range of ports
        --group-add list                 Add additional groups to join
        --health-cmd string              Command to run to check health
        --health-interval duration       Time between running the check (ms|s|m|h) (default 0s)
        --health-retries int             Consecutive failures needed to report unhealthy
        --health-start-period duration   Start period for the container to initialize before
                                         starting health-retries countdown (ms|s|m|h) (default 0s)
        --health-timeout duration        Maximum time to allow one check to run (ms|s|m|h)
                                         (default 0s)
        --help                           Print usage
    -h, --hostname string                Container host name
        --init                           Run an init inside the container that forwards signals
                                         and reaps processes
    -i, --interactive                    Keep STDIN open even if not attached
        --ip string                      IPv4 address (e.g., 172.30.100.104)
        --ip6 string                     IPv6 address (e.g., 2001:db8::33)
        --ipc string                     IPC mode to use
        --isolation string               Container isolation technology
        --kernel-memory bytes            Kernel memory limit
    -l, --label list                     Set meta data on a container
        --label-file list                Read in a line delimited file of labels
        --link list                      Add link to another container
        --link-local-ip list             Container IPv4/IPv6 link-local addresses
        --log-driver string              Logging driver for the container
        --log-opt list                   Log driver options
        --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
    -m, --memory bytes                   Memory limit
        --memory-reservation bytes       Memory soft limit
        --memory-swap bytes              Swap limit equal to memory plus swap: '-1' to enable
                                         unlimited swap
        --memory-swappiness int          Tune container memory swappiness (0 to 100) (default -1)
        --mount mount                    Attach a filesystem mount to the container
        --name string                    Assign a name to the container
        --network string                 Connect a container to a network (default "default")
        --network-alias list             Add network-scoped alias for the container
        --no-healthcheck                 Disable any container-specified HEALTHCHECK
        --oom-kill-disable               Disable OOM Killer
        --oom-score-adj int              Tune host's OOM preferences (-1000 to 1000)
        --pid string                     PID namespace to use
        --pids-limit int                 Tune container pids limit (set -1 for unlimited)
        --privileged                     Give extended privileges to this container
    -p, --publish list                   Publish a container's port(s) to the host
    -P, --publish-all                    Publish all exposed ports to random ports
        --read-only                      Mount the container's root filesystem as read only
        --restart string                 Restart policy to apply when a container exits (default
                                         "no")
        --rm                             Automatically remove the container when it exits
        --runtime string                 Runtime to use for this container
        --security-opt list              Security Options
        --shm-size bytes                 Size of /dev/shm
        --sig-proxy                      Proxy received signals to the process (default true)
        --stop-signal string             Signal to stop a container (default "SIGTERM")
        --stop-timeout int               Timeout (in seconds) to stop a container
        --storage-opt list               Storage driver options for the container
        --sysctl map                     Sysctl options (default map[])
        --tmpfs list                     Mount a tmpfs directory
    -t, --tty                            Allocate a pseudo-TTY
        --ulimit ulimit                  Ulimit options (default [])
    -u, --user string                    Username or UID (format: <name|uid>[:<group|gid>])
        --userns string                  User namespace to use
        --uts string                     UTS namespace to use
    -v, --volume list                    Bind mount a volume
        --volume-driver string           Optional volume driver for the container
        --volumes-from list              Mount volumes from the specified container(s)
    -w, --workdir string                 Working directory inside the container
```

#### Mapear volumenes y puertos
Es necesario crear el fichero [helloworld.html](docker-nginx/helloworld.html "helloworld.html") en /home/vagrant/nginx/helloworld.html

``` [bash]
$ docker run --name some-nginx -p 9080:80 -v /home/vagrant/nginx:/usr/share/nginx/html:ro -d nginx
  b1cf2a0a002afbd64cead2da62973e706e556c6688bb7c5429a7597104b9a17d

$ docker container ls -a
  CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                         PORTS                  NAMES
  b1cf2a0a002a        nginx               "nginx -g 'daemon of…"   About a minute ago   Up About a minute              0.0.0.0:9080->80/tcp   some-nginx
  0a581579fff6        nginx               "nginx -g 'daemon of…"   27 minutes ago       Up 27 minutes                  80/tcp                 relaxed_darwin
  9728096fe67c        nginx               "nginx -g 'daemon of…"   27 minutes ago       Exited (0) 27 minutes ago                             goofy_mayer
  d515982d2264        nginx               "nginx -g 'daemon of…"   About an hour ago    Exited (0) About an hour ago                          lucid_mirzakhani
  96f4ba5b5657        hello-world         "/hello"                 6 hours ago          Exited (0) 6 hours ago                                naughty_lehmann


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
