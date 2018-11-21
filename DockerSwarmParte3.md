# Ejemplos Docker Swarm

#### Gestion de servicios y stack en Swarm
Ya se ha visto que es un servicio en ecosistema docker, facilitado por docker-compose. Un stack es la parte superior de cualquier jerarquía de una aplicación distribuida. Es un grupo de servicios interelacionados que comparten dependencias y pueden ser orquestados y escalados conjuntamente.

#### Despliegue de una aplicación
El siguiente paso es el despliegue de los servicios de un stack en un cluster Swarm. Para ello, es necesario estar en un nodo manager del cluster Swarm

``` [bash]
$ docker stack --help
  Usage:	docker stack [OPTIONS] COMMAND
  Manage Docker stacks
  Options:
        --orchestrator string   Orchestrator to use
                                (swarm|kubernetes|all)

  Commands:
    deploy      Deploy a new stack or update an existing stack
    ls          List stacks
    ps          List the tasks in the stack
    rm          Remove one or more stacks
    services    List the services in the stack

$ docker stack deploy --help
  Usage:	docker stack deploy [OPTIONS] STACK
  Deploy a new stack or update an existing stack
  Aliases:
    deploy, up

  Options:
        --bundle-file string     Path to a Distributed
                                 Application Bundle file
    -c, --compose-file strings   Path to a Compose file, or "-"
                                 to read from stdin
        --orchestrator string    Orchestrator to use
                                 (swarm|kubernetes|all)
        --prune                  Prune services that are no
                                 longer referenced
        --resolve-image string   Query the registry to resolve
                                 image digest and supported
                                 platforms
                                 ("always"|"changed"|"never")
                                 (default "always")
        --with-registry-auth     Send registry authentication
                                 details to Swarm agents



$ mkdir swarm-stack
$ cd swarm-stack/
$ cp ../docker-compose/docker-compose.yml .

$ docker stack deploy -c docker-compose.yml getstartedlab
  Ignoring unsupported options: restart

  Creating network getstartedlab_devops
  Creating service getstartedlab_nginx
$ docker stack ls
  NAME                SERVICES            ORCHESTRATOR
  getstartedlab       1                   Swarm

$ docker stack ps getstartedlab
  ID                  NAME                    IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
  kowx06b60av7        getstartedlab_nginx.1   nginx:latest        master1             Running             Running 19 seconds ago                       

```

Al estar en un cluster docker, podemos acceder al servicio expuesto desde cualquier nodo del cluster, siendo transparente el nodo donde se encuentre desplegado.

``` [bash]
curl 192.168.77.21:9080/helloworld.html
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

$ curl 192.168.77.22:9080/helloworld.html
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

$ curl 192.168.77.23:9080/helloworld.html
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

$ curl 192.168.77.31:9080/helloworld.html
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


#### Actualizar un stack

En el siguiente paso se va a actualizar el stack que está corriendo en el cluster, para ello es necesario actualizar el docker-compose.yml que hemos ejecutado, añadiendo otro servicio nuevo:
``` [YAML]
 visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - devops
```

``` [bash]

$ docker stack services getstartedlab
  ID                  NAME                  MODE                REPLICAS            IMAGE               PORTS
  mwmdw4x9z8gs        getstartedlab_nginx   replicated          1/1                 nginx:latest        *:9080->80/tcp

$ docker stack deploy -c docker-compose.yml getstartedlab
  Ignoring unsupported options: restart

  Creating service getstartedlab_visualizer
  Updating service getstartedlab_nginx (id: mwmdw4x9z8gs8tlfwqv4354g7)

$ docker stack services getstartedlab
  ID                  NAME                       MODE                REPLICAS            IMAGE                             PORTS
  ibm5wvkzju0m        getstartedlab_visualizer   replicated          1/1                 dockersamples/visualizer:stable   *:8080->8080/tcp
  mwmdw4x9z8gs        getstartedlab_nginx        replicated          1/1                 nginx:latest                      *:9080->80/tcp


```

#### Replicando servicios
Para escalar los servicios se puede hacer directamente en el docker-compose.yml, con las variables en la etiqueta deploy:

    mode: replicated
    replicas: 6


``` [bash]
$ docker stack deploy -c docker-compose.yml getstartedlab
  Ignoring unsupported options: restart

  Updating service getstartedlab_nginx (id: mwmdw4x9z8gs8tlfwqv4354g7)
  Updating service getstartedlab_visualizer (id: ibm5wvkzju0mj257g2e3d3z2f)

$ docker service ls
  ID                  NAME                       MODE                REPLICAS            IMAGE                             PORTS
  mwmdw4x9z8gs        getstartedlab_nginx        replicated          2/6                 nginx:latest                      *:9080->80/tcp
  ibm5wvkzju0m        getstartedlab_visualizer   replicated          1/1                 dockersamples/visualizer:stable   *:8080->8080/tcp

```

#### Consultando el estado de un servicio
Ver la pila de tareas que se han realizado en un servicio es muy importante para detectar y anticipar anomalías en el sistema.

``` [bash]
$ docker service ps getstartedlab_nginx
  ID                  NAME                        IMAGE               NODE                DESIRED STATE       CURRENT STATE                     ERROR                              PORTS
  kowx06b60av7        getstartedlab_nginx.1       nginx:latest        master1             Running             Running about an hour ago                                            
  1bj0i5a64tfa        getstartedlab_nginx.2       nginx:latest        master1             Running             Running 26 minutes ago                                               
  0e6wngcd7v4p         \_ getstartedlab_nginx.2   nginx:latest        master2             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  p5dwa51w46vm         \_ getstartedlab_nginx.2   nginx:latest        node1               Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  qbcmgbvcgxuj         \_ getstartedlab_nginx.2   nginx:latest        master2             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  h4s5rgh85cvo         \_ getstartedlab_nginx.2   nginx:latest        node1               Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  bo9a0k2gt6d6        getstartedlab_nginx.3       nginx:latest        master2             Running             Running less than a second ago                                       
  t8m8smxywfl8         \_ getstartedlab_nginx.3   nginx:latest        node1               Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  h9b6ro6sbdya         \_ getstartedlab_nginx.3   nginx:latest        master3             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  k9c2lf0bj6t3         \_ getstartedlab_nginx.3   nginx:latest        master3             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  7vg7ouj5dyae         \_ getstartedlab_nginx.3   nginx:latest        master2             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  qykfldkyu6wn        getstartedlab_nginx.4       nginx:latest        master1             Running             Running 26 minutes ago                                               
  mwqt7lk960kd         \_ getstartedlab_nginx.4   nginx:latest        master3             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  ignw77mrgu6e         \_ getstartedlab_nginx.4   nginx:latest        master3             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  ugw5dpz8cw2e         \_ getstartedlab_nginx.4   nginx:latest        master3             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  vp8x9myz5dkk        getstartedlab_nginx.5       nginx:latest        master1             Running             Running 26 minutes ago                                               
  chgj0bka2by5         \_ getstartedlab_nginx.5   nginx:latest        node1               Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  wyvqj64wa40i         \_ getstartedlab_nginx.5   nginx:latest        master3             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  5eefgksylsxf         \_ getstartedlab_nginx.5   nginx:latest        master2             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  pqpi1qgda2uf         \_ getstartedlab_nginx.5   nginx:latest        master2             Shutdown            Rejected less than a second ago   "error while mounting volume w…"   
  i7jt7y8bk8c6        getstartedlab_nginx.6       nginx:latest        master1             Running             Running 27 minutes ago                                               
```

#### Escalando manualmente
También es posible la replicación usando los comandos de docker.

``` [bash]
$ docker service scale getstartedlab_nginx=2
  getstartedlab_nginx scaled to 2
  overall progress: 2 out of 2 tasks
  1/2: running   
  2/2: error while mounting volume with options: type='none' device='/hverify: Service converged

$ docker service scale getstartedlab_nginx=10
  getstartedlab_nginx scaled to 10
  overall progress: 10 out of 10 tasks
  1/10: running   
  2/10: running   
  3/10: running   
  4/10: running   
  5/10: running   
  6/10: running   
  7/10: running   
  8/10: running   
  9/10: running   
  10/10: running   
  verify: Service converged

```
