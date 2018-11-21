# Ejemplos Docker Swarm


#### Introducción
Al igúal que es posible arrancar uno o varios servicios (Multi-container) en una instancia con docker engine, docker proporciona una herramienta que permite generar un cluster, swarm (multi-machine).


#### Entendiendo docker swarm
Swarm es un grupo de maquinas con Docker instalado y configurado como cluster. Una vez que se dispone del cluster, es posible seguir ejecutando los comandos Docker que se han visto hasta ahora, o, gestionarlo mediante un *swarm manager*

Las máquinas que formarán parte del swarm pueden ser físicas y virtuales.

Los comandos que se usan para la gestión del cluster son:
``` [bash]
$ docker swarm --help
  Usage:	docker swarm COMMAND
  Manage Swarm
  Commands:
    ca          Display and rotate the root CA
    init        Initialize a swarm
    join        Join a swarm as a node and/or manager
    join-token  Manage join tokens
    leave       Leave the swarm
    unlock      Unlock swarm
    unlock-key  Manage the unlock key
    update      Update the swarm

  Run 'docker swarm COMMAND --help' for more information on a command.

$ docker node --help
  Usage:	docker node COMMAND
  Manage Swarm nodes
  Commands:
    demote      Demote one or more nodes from manager in the swarm
    inspect     Display detailed information on one or more nodes
    ls          List nodes in the swarm
    promote     Promote one or more nodes to manager in the swarm
    ps          List tasks running on one or more nodes, defaults to current node
    rm          Remove one or more nodes from the swarm
    update      Update a node

  Run 'docker node COMMAND --help' for more information on a command.

```

#### Iniciando el cluster
Para configurar el cluster de swarm se va a necesitar disponer de las cuatro máquinas virtuales corriendo. La topología del cluster va a ser:
* 3 nodos manager
* 1 nodo worker

Como primer paso, es necesario diponer de cuatro terminales conectados a cada instancia.

###### Swarm-master1

En swarm-master1, como se ha utilizado anteriormente, es necesario comprobar los contenedores que se estén ejecutando, pararlos y borrarlos para disponer de una instancia más limpia.
``` [bash]
$ docker ps -a
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                    PORTS                  NAMES
  0f19ba2eb008        nginx               "nginx -g 'daemon of…"   About an hour ago   Up About an hour          0.0.0.0:9080->80/tcp   docker-compose_nginx_1_3eb64d3de1db
  9dd5bc6e4f1a        my-nginx            "nginx -g 'daemon of…"   3 hours ago         Exited (0) 2 hours ago                           my-nginx
  b1cf2a0a002a        nginx               "nginx -g 'daemon of…"   5 hours ago         Exited (0) 2 hours ago                           some-nginx
  0a581579fff6        nginx               "nginx -g 'daemon of…"   5 hours ago         Exited (0) 2 hours ago                           relaxed_darwin
  9728096fe67c        nginx               "nginx -g 'daemon of…"   5 hours ago         Exited (0) 5 hours ago                           goofy_mayer
  d515982d2264        nginx               "nginx -g 'daemon of…"   5 hours ago         Exited (0) 5 hours ago                           lucid_mirzakhani
  96f4ba5b5657        hello-world         "/hello"                 11 hours ago        Exited (0) 11 hours ago                          naughty_lehmann
$ docker stop 0f19ba2eb008
  0f19ba2eb008
$ docker container rm -vf 0f19ba2eb008 9dd5bc6e4f1a b1cf2a0a002a 0a581579fff6 9728096fe67c d515982d2264 96f4ba5b5657
  0f19ba2eb008
  9dd5bc6e4f1a
  b1cf2a0a002a
  0a581579fff6
  9728096fe67c
  d515982d2264
  96f4ba5b5657
$ docker ps -a
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

```
Una vez que esta el demonio de docker preparado, se ejecuta el comando:

``` [bash]
$ docker swarm init --advertise-addr <myvm1 ip>
$ docker swarm init --advertise-addr 192.168.77.21
Swarm initialized: current node (sv9frfpb2nh2h7md8lf4m2hfc) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-5788ew3k3bm4j0rdqx5qmohhl632wxcfskv61tjwde1i0h223l-5tpdarl6ll0ow0267zploohsa 192.168.77.21:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```
Es interesante ver la información que nos proporciona la información de docker
``` [bash]
$ docker info
  ...
  Swarm: active
   NodeID: sv9frfpb2nh2h7md8lf4m2hfc
   Is Manager: true
   ClusterID: mact1emkbtwnse330w9fp5uia
   Managers: 1
   Nodes: 1
   Default Address Pool: 10.0.0.0/8  
   SubnetSize: 24
   Orchestration:
    Task History Retention Limit: 5
   Raft:
    Snapshot Interval: 10000
    Number of Old Snapshots to Retain: 0
    Heartbeat Tick: 1
    Election Tick: 10
   Dispatcher:
    Heartbeat Period: 5 seconds
   CA Configuration:
    Expiry Duration: 3 months
    Force Rotate: 0
   Autolock Managers: false
   Root Rotation In Progress: false
   Node Address: 192.168.77.21
   Manager Addresses:
    192.168.77.21:2377
  ...
```

Para saber los comandos que se deben de ejecutar para añdir los otros nodos manager o workers se debe de ejecutar:
``` [bash]
$ docker swarm join-token manager
  To add a manager to this swarm, run the following command:

      docker swarm join --token SWMTKN-1-5788ew3k3bm4j0rdqx5qmohhl632wxcfskv61tjwde1i0h223l-3udzylr18q1ygsn6kifk65gjj 192.168.77.21:2377

$ docker swarm join-token worker
  To add a worker to this swarm, run the following command:

      docker swarm join --token SWMTKN-1-5788ew3k3bm4j0rdqx5qmohhl632wxcfskv61tjwde1i0h223l-5tpdarl6ll0ow0267zploohsa 192.168.77.21:2377

```

###### swarm-master2
``` [bash]
$ docker swarm join --token SWMTKN-1-5788ew3k3bm4j0rdqx5qmohhl632wxcfskv61tjwde1i0h223l-3udzylr18q1ygsn6kifk65gjj 192.168.77.21:2377
  This node joined a swarm as a manager.

$ docker node ls
  ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
  sv9frfpb2nh2h7md8lf4m2hfc     master1             Ready               Active              Leader              18.09.0
  u7502q9kiubkpxxwy57lhln9m *   master2             Ready               Active              Reachable           18.09.0

```

###### swarm-master3
``` [bash]
$ docker swarm join --token SWMTKN-1-5788ew3k3bm4j0rdqx5qmohhl632wxcfskv61tjwde1i0h223l-3udzylr18q1ygsn6kifk65gjj 192.168.77.21:2377
  This node joined a swarm as a manager.

$ docker node ls
  ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
  sv9frfpb2nh2h7md8lf4m2hfc     master1             Ready               Active              Leader              18.09.0
  u7502q9kiubkpxxwy57lhln9m     master2             Ready               Active              Reachable           18.09.0
  y3xy6dx4lcz93i9p96p54uq1o *   master3             Ready               Active              Reachable           18.09.0

```
###### swarm-node1
``` [bash]
$ docker swarm join --token SWMTKN-1-5788ew3k3bm4j0rdqx5qmohhl632wxcfskv61tjwde1i0h223l-5tpdarl6ll0ow0267zploohsa 192.168.77.21:2377
  This node joined a swarm as a worker.

$ docker node ls
  Error response from daemon: This node is not a swarm manager. Worker nodes can't be used to view or modify cluster state. Please run this command on a manager node or promote the current node to a manager.

```

#### Estado del cluster y de los nodos
Para saber el estado del cluster, se dispone de estos comandos

``` [bash]
$ docker node ls  
  ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
  sv9frfpb2nh2h7md8lf4m2hfc *   master1             Ready               Active              Leader              18.09.0
  u7502q9kiubkpxxwy57lhln9m     master2             Ready               Active              Reachable           18.09.0
  y3xy6dx4lcz93i9p96p54uq1o     master3             Ready               Active              Reachable           18.09.0
  e1cmg5tidccoolkpgylnn2boi     node1               Ready               Active                                  18.09.0

$ docker node inspect node1
  [
      {
          "ID": "e1cmg5tidccoolkpgylnn2boi",
          "Version": {
              "Index": 27
          },
          "CreatedAt": "2018-11-14T02:55:56.914989154Z",
          "UpdatedAt": "2018-11-14T02:55:56.997531388Z",
          "Spec": {
              "Labels": {},
              "Role": "worker",
              "Availability": "active"
          },
          "Description": {
              "Hostname": "node1",
              "Platform": {
                  "Architecture": "x86_64",
                  "OS": "linux"
              },
              "Resources": {
                  "NanoCPUs": 1000000000,
                  "MemoryBytes": 1927991296
              },
              "Engine": {
                  "EngineVersion": "18.09.0",
                  "Plugins": [
                      {
                          "Type": "Log",
                          "Name": "awslogs"
                      },
                      {
                          "Type": "Log",
                          "Name": "fluentd"
                      },
                      {
                          "Type": "Log",
                          "Name": "gcplogs"
                      },
                      {
                          "Type": "Log",
                          "Name": "gelf"
                      },
                      {
                          "Type": "Log",
                          "Name": "journald"
                      },
                      {
                          "Type": "Log",
                          "Name": "json-file"
                      },
                      {
                          "Type": "Log",
                          "Name": "local"
                      },
                      {
                          "Type": "Log",
                          "Name": "logentries"
                      },
                      {
                          "Type": "Log",
                          "Name": "splunk"
                      },
                      {
                          "Type": "Log",
                          "Name": "syslog"
                      },
                      {
                          "Type": "Network",
                          "Name": "bridge"
                      },
                      {
                          "Type": "Network",
                          "Name": "host"
                      },
                      {
                          "Type": "Network",
                          "Name": "macvlan"
                      },
                      {
                          "Type": "Network",
                          "Name": "null"
                      },
                      {
                          "Type": "Network",
                          "Name": "overlay"
                      },
                      {
                          "Type": "Volume",
                          "Name": "local"
                      }
                  ]
              },
              "TLSInfo": {
                  "TrustRoot": "-----BEGIN CERTIFICATE-----\nMIIBajCCARCgAwIBAgIUAZj+Q8/k3akVWf1+KRwwd2/SRZ4wCgYIKoZIzj0EAwIw\nEzERMA8GA1UEAxMIc3dhcm0tY2EwHhcNMTgxMTEzMjE1ODAwWhcNMzgxMTA4MjE1\nODAwWjATMREwDwYDVQQDEwhzd2FybS1jYTBZMBMGByqGSM49AgEGCCqGSM49AwEH\nA0IABDXQRXc/Hws7Ah8RCz1bTbpiCo+FoNGsflsR7Orfx3WxVA1zMmVlJ2035UCL\nYfROuP4y/0yTJ9zLkguJUBbrGQWjQjBAMA4GA1UdDwEB/wQEAwIBBjAPBgNVHRMB\nAf8EBTADAQH/MB0GA1UdDgQWBBTvbtBwf4QdgKDh/djrl5touqpC4DAKBggqhkjO\nPQQDAgNIADBFAiEA6tE4CyfEwZg6S/S4sZgKOKRhFONN4u47Mt2bykXAV5ECIH7M\nIxRUEK5u1OkGgZpcstXwilXs31Ndz1sMAHiWXHVl\n-----END CERTIFICATE-----\n",
                  "CertIssuerSubject": "MBMxETAPBgNVBAMTCHN3YXJtLWNh",
                  "CertIssuerPublicKey": "MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAENdBFdz8fCzsCHxELPVtNumIKj4Wg0ax+WxHs6t/HdbFUDXMyZWUnbTflQIth9E64/jL/TJMn3MuSC4lQFusZBQ=="
              }
          },
          "Status": {
              "State": "ready",
              "Addr": "192.168.77.31"
          }
      }
  ]


```
