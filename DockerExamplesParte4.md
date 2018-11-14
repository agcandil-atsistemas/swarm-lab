# Ejemplos Docker

## Parte 4: Construcción de imagenes

---

#### Dockerfile
Para este punto se va a trabajar con el [Dockerfile](docker-nginx/Dockerfile "nginx Dockerfile") que se encuentra en la carpeta docker-nginx.
Es necesario disponer replicada esta carpeta con su contenido:
* Dockerfile
* helloworld.html


#### Construir una imagen
Para construir una imagen es necesario situarse en el directorio donde se encuentra el Dockerfile. Este ejemplo es muy sencillo, lo único que se realiza es, partiendo de la imagen base de nginx, generar una nueva imagen con el html de ejemplo, lo que evita que sea necesario mapear posteriormente el volumen con el fichero.

``` [Dockerfile]
FROM nginx
COPY helloworld.html /usr/share/nginx/html/helloworld.html
```

Para ejecutar una construcción se ejecuta el comando build
``` [bash]
$ pwd
  /home/vagrant
$ ls
  nginx

$ cp -R nginx docker-nginx
$ ls
  docker-nginx  nginx

$ cd docker-nginx/
$ vi Dockerfile
$ ls
  Dockerfile  helloworld.html

$ docker build --help

  Usage:	docker build [OPTIONS] PATH | URL | -

  Build an image from a Dockerfile

  Options:
        --add-host list           Add a custom host-to-IP mapping (host:ip)
        --build-arg list          Set build-time variables
        --cache-from strings      Images to consider as cache sources
        --cgroup-parent string    Optional parent cgroup for the container
        --compress                Compress the build context using gzip
        --cpu-period int          Limit the CPU CFS (Completely Fair Scheduler) period
        --cpu-quota int           Limit the CPU CFS (Completely Fair Scheduler) quota
    -c, --cpu-shares int          CPU shares (relative weight)
        --cpuset-cpus string      CPUs in which to allow execution (0-3, 0,1)
        --cpuset-mems string      MEMs in which to allow execution (0-3, 0,1)
        --disable-content-trust   Skip image verification (default true)
    -f, --file string             Name of the Dockerfile (Default is 'PATH/Dockerfile')
        --force-rm                Always remove intermediate containers
        --iidfile string          Write the image ID to the file
        --isolation string        Container isolation technology
        --label list              Set metadata for an image
    -m, --memory bytes            Memory limit
        --memory-swap bytes       Swap limit equal to memory plus swap: '-1' to enable unlimited swap
        --network string          Set the networking mode for the RUN instructions during build
                                  (default "default")
        --no-cache                Do not use cache when building the image
        --pull                    Always attempt to pull a newer version of the image
    -q, --quiet                   Suppress the build output and print image ID on success
        --rm                      Remove intermediate containers after a successful build (default true)
        --security-opt strings    Security options
        --shm-size bytes          Size of /dev/shm
    -t, --tag list                Name and optionally a tag in the 'name:tag' format
        --target string           Set the target build stage to build.
        --ulimit ulimit           Ulimit options (default [])

$ docker build . -t my-nginx:latest
  Sending build context to Docker daemon  3.072kB
  Step 1/2 : FROM nginx
   ---> 62f816a209e6
  Step 2/2 : COPY helloworld.html /usr/share/nginx/html/helloworld.html
   ---> e93418e9b01c
  Successfully built e93418e9b01c
  Successfully tagged my-nginx:latest
$ docker image ls
  REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
  my-nginx            latest              e93418e9b01c        3 seconds ago       109MB
  nginx               latest              62f816a209e6        6 days ago          109MB
  hello-world         latest              4ab4c602aa5e        2 months ago        1.84kB


```  

Si comparamos las capas de las dos imágenes de nginx podemos comprobar:
* nginx:

      "Layers": [
            "sha256:237472299760d6726d376385edd9e79c310fe91d794bc9870d038417d448c2d5",
            "sha256:6ccbee34dd1052580674858262f7f29e9c1be9f0eda5bfdf79b6045faf030af7",
            "sha256:ad9ac0e6043bccdcbac25fdbfe849c643e2cff5f28be6468603b16a3c805ecdd"
        ]


  * my-nginx:

        "Layers": [
                "sha256:237472299760d6726d376385edd9e79c310fe91d794bc9870d038417d448c2d5",
                "sha256:6ccbee34dd1052580674858262f7f29e9c1be9f0eda5bfdf79b6045faf030af7",
                "sha256:ad9ac0e6043bccdcbac25fdbfe849c643e2cff5f28be6468603b16a3c805ecdd",
                "sha256:6260090cd66d931b74e0ed9f51a0b14ce2308f9125f1714150ddf3fe984073c5"
            ]

Otro comando que aporta gran información es ** image history **

``` [bash]
$ docker image history my-nginx:latest
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
e93418e9b01c        3 minutes ago       /bin/sh -c #(nop) COPY file:2404ecaf74ac093f…   229B                
62f816a209e6        6 days ago          /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B                  
<missing>           6 days ago          /bin/sh -c #(nop)  STOPSIGNAL [SIGTERM]         0B                  
<missing>           6 days ago          /bin/sh -c #(nop)  EXPOSE 80/tcp                0B                  
<missing>           6 days ago          /bin/sh -c ln -sf /dev/stdout /var/log/nginx…   22B                 
<missing>           6 days ago          /bin/sh -c set -x  && apt-get update  && apt…   53.8MB              
<missing>           6 days ago          /bin/sh -c #(nop)  ENV NJS_VERSION=1.15.6.0.…   0B                  
<missing>           6 days ago          /bin/sh -c #(nop)  ENV NGINX_VERSION=1.15.6-…   0B                  
<missing>           4 weeks ago         /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B                  
<missing>           4 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B                  
<missing>           4 weeks ago         /bin/sh -c #(nop) ADD file:f8f26d117bc4a9289…   55.3MB      

```

#### Crear un tag de una imagen
Es importante gestionar las versiones de las imagenes con las que se trabaja. Para ello existe el comando tag

``` [bash]
$ docker image tag --help

  Usage:	docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

  Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

$ docker image tag my-nginx:latest my-nginx:1.0.0

$ docker image ls
  REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
  my-nginx            1.0.0               e93418e9b01c        16 minutes ago      109MB
  my-nginx            latest              e93418e9b01c        16 minutes ago      109MB
  nginx               latest              62f816a209e6        6 days ago          109MB
  hello-world         latest              4ab4c602aa5e        2 months ago        1.84kB

```

#### Publicar una imagen en un registry

Para poder publicar una imagen en cualquier registry que albergue imagenes docker es necesario crear un tag indicando la url del registry, hacer login si está securizado y hacer push de la imagen.


``` [bash]
$ docker image tag my-nginx:latest atsagc/my-nginx:latest

$ docker login -u username -p *****  docker.io
  WARNING! Using --password via the CLI is insecure. Use --password-stdin.
  WARNING! Your password will be stored unencrypted in /home/vagrant/.docker/config.json.
  Configure a credential helper to remove this warning. See
  https://docs.docker.com/engine/reference/commandline/login/#credentials-store

  Login Succeeded

$ docker push atsagc/my-nginx:latest
  The push refers to repository [docker.io/atsagc/my-nginx]
  6260090cd66d: Pushed
  ad9ac0e6043b: Mounted from library/nginx
  6ccbee34dd10: Mounted from library/nginx
  237472299760: Mounted from library/nginx
  latest: digest: sha256:cba2f48a74c5823890c646a90d75243f4f76f6d66e8a696da87c5003c6063fb4 size: 1155

```

Podemos ver la imagen publicada en:

https://hub.docker.com/r/atsagc/my-nginx/

#### Arrancamos el contenedor

``` [bash]
$ docker run --name my-nginx -p 9081:80 -d my-nginx
  9dd5bc6e4f1a3601e68c0ebcf5aa0256e36e1be4a33e1655cbb4cfcc21d045f6

$ curl localhost:9081/helloworld.html
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
