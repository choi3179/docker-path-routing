# docker nginx vhost

![image](https://github.com/pySatellite/docker-nginx-vhost/assets/87309910/878eaf6a-18bc-4467-8b3f-5086de8ff3a1)

## load-balancing
- https://www.nginx.com/resources/glossary/load-balancing/

## Process
### build & push
```
$ tree
.
├── README.md
├── lb
│   ├── Dockerfile
│   └── config
│       └── default.conf
├── serv-a
│   ├── Dockerfile
│   └── index.html
└── serv-b
    ├── Dockerfile
    └── index.html

$ cd lb
$ sudo docker build -t pysatellite/lb:0.1.0 .

$ cd ../serv-a
$ sudo docker build -t serv-a:0.1.0 .

$ cd ../serv-b
$ sudo docker build -t serv-b:0.1.0 .

```

### Run
```
$ sudo docker network create -d bridge lb-net
$ sudo docker run -d --name serv-a --network lb-net serv-a:0.1.0
$ sudo docker run -d --name serv-b --network lb-net serv-b:0.1.0
$ sudo docker run -d -p 8001:80 --name lb --network lb-net lb:0.1.0
```

### CHECK
```
$ sudo docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED              STATUS              PORTS                                   NAMES
d86886fc9d9e   pysatellite/lb:0.1.0       "/docker-entrypoint.…"   3 seconds ago        Up 2 seconds        0.0.0.0:8001->80/tcp, :::8001->80/tcp   lb
e4444ea35c22   pysatellite/serv-b:0.1.0   "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp                                  serv-b
dd8af81cafb7   pysatellite/serv-a:0.1.0   "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp                                  serv-a
```

### Success
```
$ curl http://localhost:8001
<h1>A</h1>
$ curl http://localhost:8001
<h1>B</h1>
$ curl http://localhost:8001
<h1>A</h1>
```

## ref
- https://hub.docker.com/
