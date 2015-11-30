# Infinite Scalable System with Docker & Docker Machine & Docker Swarm
This is a sample project to scale a simple Node.js application to infinity. Infinity is bounded by your resources.

## How to Setup Entire Cluster
I assume that you have already installed Docker and Related Technologies. If not, please refer [here](https://www.docker.com/docker-toolbox) to install
You can do following steps to setup cluster after cloned this repository.

1. ```docker-machine create -d virtualbox local```
2. ```eval $(docker-machine env local)```
3. ```docker run swarm create``` . You will be returned a token on step 3. And please use that token on following sections. 
4. ```docker-machine create -d virtualbox --swarm--swarm-master --swarm-discovery token://<TOKEN> swarm-master```
5. ```docker-machine create -d virtualbox --swarm--swarm-discovery token://<TOKEN> swarm-agent-1```
6. ```docker-machine create -d virtualbox --swarm--swarm-discovery token://<TOKEN> swarm-agent-2```
7. ```docker-machine create -d virtualbox --swarm--swarm-discovery token://<TOKEN> swarm-agent-3```
8. ```eval $(docker-machine env swarm-agent-1)```
9. In this step we will build docker-gen image, but you need to copy your certificate files for swarm-agent-1 machine to docker-gen/certs folder
    a.  ```cp /path/to/user/folder/.docker/machine/machines/swarm-agent-01/ca.pem docker-gen/certs/```
    b. ```cp /path/to/user/folder/.docker/machine/machines/swarm-agent-01/cert.pem docker-gen/certs/```
    c. ```cp /path/to/user/folder/.docker/machine/machines/swarm-agent-01/key.pem docker-gen/certs/```
    d. ```docker build -t docker-gen docker-gen/.```
10. ```docker run -d --restart=always --name=docker-gen --env=affinity:image==docker-gen:latest --env=DOCKER_HOST=tcp://<IP_OF_SWARM_MASTER_MACHINE>:3376 docker-gen```
11. ```docker build -t nginx nginx/.```
12. ```docker run -d --restart=always --name=nginx -p 80:80 --volumes-from=docker-gen nginx```
13. ```docker build -t express-hello express-hello/.```
14. ```docker run -d --restart=always -p 3000 --name api1 -P --env VHOST=api.nodejs.com express-hello```

In order to see the status of the entire cluster

1. ```eval $(docker-machine env --swarm swarm-master)```
2. ```docker info```

In order to see all running containers inside cluster

1. ```eval $(docker-machine env --swarm swarm-master)```
2. ```docker ps```


