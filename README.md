# Docker Mastery

## Creating and Using Containers Like a Boss

```bash
➜  docker version
Client:
Version:           18.06.1-ce
API version:       1.38
Go version:        go1.10.3
Git commit:        e68fc7a
Built:             Tue Aug 21 17:21:31 2018
OS/Arch:           darwin/amd64
Experimental:      false

Server:
Engine:
  Version:          18.06.1-ce
  API version:      1.38 (minimum version 1.12)
  Go version:       go1.10.3
  Git commit:       e68fc7a
  Built:            Tue Aug 21 17:29:02 2018
  OS/Arch:          linux/amd64
  Experimental:     true

docker info
docker <command> <sub-command> (options)
```

### Starting NginX Web Server

```bash
➜  ~ docker container run --publish 80:80 nginx

➜  ~ docker container run --publish 80:80 --detach nginx
72f828592f46e58da3755aa89736db5751386e16461a167cbd0078068c5369d5
```
### List running containers

```bash
➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                NAMES
72f828592f46        nginx               "nginx -g 'daemon of…"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp   heuristic_vaughan
```

### Docker container stop

```bash
➜  ~ docker container stop 72f8
```

### Run vs. Start

```bash
➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
72f828592f46        nginx               "nginx -g 'daemon of…"   26 minutes ago      Exited (0) 40 seconds ago                       heuristic_vaughan
5c01ea8f4aee        nginx               "nginx -g 'daemon of…"   28 minutes ago      Exited (0) 26 minutes ago                       mystifying_lalande

'docker container run’ always starts a *new* container
Use ‘docker container start’ to start an existing stopped one 

➜  ~ docker container run --publish 80:80 --detach --name webhost nginx
875fab0a285a0786c0cf8dec867d82f7dd439304f2e2612210923190eac15f6e
➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS                NAMES
875fab0a285a        nginx               "nginx -g 'daemon of…"   27 seconds ago      Up 26 seconds               0.0.0.0:80->80/tcp   webhost
72f828592f46        nginx               "nginx -g 'daemon of…"   30 minutes ago      Exited (0) 5 minutes ago                         heuristic_vaughan
5c01ea8f4aee        nginx               "nginx -g 'daemon of…"   32 minutes ago      Exited (0) 31 minutes ago                        mystifying_lalande
```

### Docker container logs

```bash
➜  ~ docker container logs webhost
172.17.0.1 - - [11/Sep/2018:11:20:34 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 Safari/537.36" "-"
172.17.0.1 - - [11/Sep/2018:11:20:35 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.81 Safari/537.36" “-"

➜  ~ docker container top webhost
PID                 USER                TIME                COMMAND
2571                root                0:00                nginx: master process nginx -g daemon off;
2604                101                 0:00                nginx: worker process
```

### Docker container rm

```bash
➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
875fab0a285a        nginx               "nginx -g 'daemon of…"   6 minutes ago       Up 6 minutes        0.0.0.0:80->80/tcp   webhost
➜  ~ docker container rm -f 875f
875f
```

## Container VS. VM: It’s Just a Process

```bash
➜  ~ docker run --name mongo -d mongo
➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
72098970a4ff        mongo               "docker-entrypoint.s…"   59 seconds ago      Up 58 seconds       27017/tcp           mongo
➜  ~ docker top mongo
PID                 USER                TIME                COMMAND
2893                999                 0:01                mongod —bind_ip_all
➜  ~ docker stop mongodocker
mongo
➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
➜  ~ docker top mongo
Error response from daemon: Container 72098970a4ff305bb3e4e4941239bff5a5467db39681924386ca18560fa728c1 is not running
```

## Assignment: Manage Multiple Containers

```bash
➜  ~ docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
f9b09ad4930a634985f78ab88d4482f2e1e14520f00e56b9238cec3af8c5ee76
➜  ~ docker container logs db
GENERATED ROOT PASSWORD: aiF5bahshieVuu6peex8esahraenie5o

➜  ~ docker container run -d --name webserver -p 8080:80 httpd
487f21cb11a9434e0049e062be11d38ff8c3b4377b6dccfb2e68a6a0004d8cc8

➜  ~ docker container run -d --name proxy -p 80:80 nginx
59e4768b80e3117564aa00b2888fb8114ff8e5f42428d19d1d8d899017e9a13b

➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                               NAMES
59e4768b80e3        nginx               "nginx -g 'daemon of…"   29 seconds ago       Up 28 seconds       0.0.0.0:80->80/tcp                  proxy
487f21cb11a9        httpd               "httpd-foreground"       About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp                webserver
f9b09ad4930a        mysql               "docker-entrypoint.s…"   4 minutes ago        Up 3 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp   db

➜  ~ docker container stop 59e4768b80e3 487f21cb11a9 f9b09ad4930a
59e4768b80e3
487f21cb11a9
f9b09ad4930a

➜  ~ docker container rm 59e4 487f f9b0
59e4
487f
f9b0
```

## What’s Going On In Containers: CLI Process Mornitering

```bash
➜  ~ docker container run -d --name nginx nginx
211909cd6753028d15c10662086d8136e4467cbce5df5cb49edfa91001038581
➜  ~ docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql
3a742a7a0098ff8fc40845046d6e3afa0e49aa8421534cc576f1f2e0aa465f58

➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
3a742a7a0098        mysql               "docker-entrypoint.s…"   41 seconds ago      Up 39 seconds       3306/tcp, 33060/tcp   mysql
211909cd6753        nginx               "nginx -g 'daemon of…"   2 minutes ago       Up 2 minutes        80/tcp                nginx
```

### Process list in one container

```bash
➜  ~ docker container top mysql
PID                 USER                TIME                COMMAND
6499                999                 0:01                mysqld

➜  ~ docker container top nginx
PID                 USER                TIME                COMMAND
6296                root                0:00                nginx: master process nginx -g daemon off;
6329                101                 0:00                nginx: worker process
```

### Details of one container config

```bash
➜  ~ docker container inspect mysql
…
```

### Performance stats for all containers

```bash
➜  ~ docker container stats —help
➜  ~ docker container stats
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
3a742a7a0098        mysql               0.83%               373.8MiB / 1.952GiB   18.70%              998B / 0B           2.61MB / 1.29GB     36
211909cd6753        nginx               0.00%               1.895MiB / 1.952GiB   0.09%               1.18kB / 0B         0B / 0B             2
```

## Getting a Shell Inside Containers

### Start new container interactively
docker container run -it  

```bash
➜  ~ docker container run -it --name proxy nginx bash
root@2f7bf298908a:/# exit
➜  ~ docker container run -it --name ubuntu ubuntu
root@e4075bbdc264:/# exit

➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
3a742a7a0098        mysql               "docker-entrypoint.s…"   22 minutes ago      Up 22 minutes       3306/tcp, 33060/tcp   mysql
211909cd6753        nginx               "nginx -g 'daemon of…"   24 minutes ago      Up 24 minutes       80/tcp

➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                      PORTS                 NAMES
e4075bbdc264        ubuntu              "/bin/bash"              About a minute ago   Exited (0) 25 seconds ago                         ubuntu
2f7bf298908a        nginx               "bash"                   4 minutes ago        Exited (0) 3 minutes ago                          proxy
3a742a7a0098        mysql               "docker-entrypoint.s…"   22 minutes ago       Up 22 minutes               3306/tcp, 33060/tcp   mysql
211909cd6753        nginx               "nginx -g 'daemon of…"   24 minutes ago       Up 24 minutes               80/tcp                nginx


➜  ~ docker container start --help

Usage:    docker container start [OPTIONS] CONTAINER [CONTAINER...]

Start one or more stopped containers

Options:
  -a, --attach                  Attach STDOUT/STDERR and forward signals
      --checkpoint string       Restore from this checkpoint
      --checkpoint-dir string   Use a custom checkpoint storage directory
      --detach-keys string      Override the key sequence for detaching a container
  -i, --interactive             Attach container's STDIN
➜  ~ docker container start -ai ubuntu
```

### Run additional command in existing container
docker container exec -it 

### Run additional process in running container

```bash
➜  ~ docker container exec -it mysql bash
root@3a742a7a0098:/# exit
exit
➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
3a742a7a0098        mysql               "docker-entrypoint.s…"   28 minutes ago      Up 28 minutes       3306/tcp, 33060/tcp   mysql
211909cd6753        nginx               "nginx -g 'daemon of…"   30 minutes ago      Up 30 minutes       80/tcp                nginx
```
### Different Linux distros in containers

```bash
➜  ~ docker pull alpine
➜  ~ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
alpine              latest              196d12cf6ab1        3 hours ago         4.41MB
➜  ~ docker container run -it alpine sh
/ #
```

## Docker Networks: Concepts
### -p (—publish) HOST:CONTAINER

```bash
➜  ~ docker container run -p 80:80 --name webhost -d nginx
42b45fcf1fb591d30f40083719af06fe9a2c855d681f302474f9d5b5773ff2da
➜  ~ docker container port webhost
80/tcp -> 0.0.0.0:80
```

### —format

```bash
➜  ~ docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost
172.17.0.4
```

## Docker Networks: CLI Management

### —network bridge
Default Docker virtual network, which is NAT’ed behind the Host IP.

### —network host
It gains performance by skipping virtual networks but sacrifices security of container model

### Show networks

```bash
➜  ~ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
99872c644479        bridge              bridge              local
6996509036e0        compose_default     bridge              local
bffb71795310        host                host                local
1e15c3367812        none
```

### Inspect a network

```bash
➜  ~ docker network inspect bridge
…
```

### Create a network

```bash
➜  ~ docker network create my_app_net
1cc36f51375a4654fb95d1d7fad25c5adbe60425b56c6ae601ef4783235abb19

➜  ~ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
1cc36f51375a        my_app_net          bridge              local

➜  ~ docker network create —help

➜  ~ docker container run -d --name new_nginx --network my_app_net nginx:alpine
2e399127b744d8de277288d6de1291754c07f07494c424bc7080abbc08a253ea
➜  ~ docker network inspect my_app_net
…
```

### Attach a network to container 

```bash
➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
42b45fcf1fb5        nginx               "nginx -g 'daemon of…"   45 minutes ago      Up 45 minutes       0.0.0.0:80->80/tcp    webhost

➜  ~ docker network connect 1cc36f51375a 42b45fcf1fb5
➜  ~ docker container inspect 42b45fcf1fb5
...
```

### Detach a network from container

```bash
➜  ~ docker network disconnect 1cc36f51375a 42b45fcf1fb5
➜  ~ docker container inspect 42b45fcf1fb5
…
```

### Docker Networks: DNS and How Containers Find Each Other

Docker defaults the hostname to the container’s name, but you can also set aliases

```bash
➜  ~ docker container run -d --name my_nginx --network my_app_net nginx:alpine
b67e64477d5ba5bc571fefed84fd5b0cfa2ffb77c927c8f9280fee1da57a48f7

➜  ~ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
f1f0758d37ac        my_app_net          bridge              local

➜  ~ docker network inspect f1f0758d37ac
…

➜  ~ docker container exec -it my_nginx ping new_nginx
PING new_nginx (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.385 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.159 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.300 ms

➜  ~ docker container exec -it new_nginx ping my_nginx
PING my_nginx (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.164 ms
64 bytes from 172.18.0.3: seq=1 ttl=64 time=0.229 ms
64 bytes from 172.18.0.3: seq=2 ttl=64 time=0.158 ms
```

### Assignment: Using Containers for CLI Testing

```bash
➜  ~ docker container run --rm -it centos:7 bash
[root@ae3b677af65f /]# exit

➜  ~ docker container run --rm -it ubuntu:16.04 bash
root@aa201fb16aed:/# exit

➜  ~ docker ps -a

Centos and ubuntu disappear —rm
```

### Assignment: DNS Round Robin Test

- Know how to user -it get shell in container
- Understand basics to what a Linux distribution is like Ubuntu and CentOS
- Know how to run a container
- Understand basics of DNS records

```bash
➜  ~ docker network create dude
4e61f1e4140f822717a753a90bfa1f127adc2c42059886a27ecd8d3a067a049b

➜  ~ docker container run -d --net dude --net-alias search elasticsearch:2
db52ebe454dcf6d93224924d609257c645b25643003b160fee93fa1bf490a1f9

➜  ~ docker container run -d --net dude --net-alias search elasticsearch:2
bd0ba831c50ae875c5b1216dbe88ea0e028bdf209703247c5d5b25830fff4dc1

➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
bd0ba831c50a        elasticsearch:2     "/docker-entrypoint.…"   20 seconds ago      Up 19 seconds       9200/tcp, 9300/tcp         hardcore_aryabhata
db52ebe454dc        elasticsearch:2     "/docker-entrypoint.…"   36 seconds ago      Up 35 seconds       9200/tcp, 9300/tcp         stoic_shockley

➜  ~ docker container run --rm --net dude alpine nslookup search
nslookup: can't resolve '(null)': Name does not resolve

Name:      search
Address 1: 172.19.0.2 search.dude
Address 2: 172.19.0.3 search.dude


➜  ~ docker container run --rm --net dude centos curl -s search:9200
{
  "name" : "James Howlett",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "VdcaBN8dSMG4bJ1yMhislQ",
  "version" : {
    "number" : "2.4.6",
    "build_hash" : "5376dca9f70f3abef96a77f4bb22720ace8240fd",
    "build_timestamp" : "2017-07-18T12:17:44Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.4"
  },
  "tagline" : "You Know, for Search"
}

➜  ~ docker container run --rm --net dude centos curl -s search:9200
{
  "name" : "Nicholas Scratch",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "Z8HTm7jBT2CzXOhkuX6tHg",
  "version" : {
    "number" : "2.4.6",
    "build_hash" : "5376dca9f70f3abef96a77f4bb22720ace8240fd",
    "build_timestamp" : "2017-07-18T12:17:44Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.4"
  },
  "tagline" : "You Know, for Search"
}

➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
bd0ba831c50a        elasticsearch:2     "/docker-entrypoint.…"   5 minutes ago       Up 5 minutes        9200/tcp, 9300/tcp         hardcore_aryabhata
db52ebe454dc        elasticsearch:2     "/docker-entrypoint.…"   5 minutes ago       Up 5 minutes        9200/tcp, 9300/tcp         stoic_shockley

➜  ~ docker container rm -f hardcore_aryabhata stoic_shockley
hardcore_aryabhata
stoic_shockley
```
