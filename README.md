# How to Host Multiple Application like NextCloud And WordPress using Nginx Reverse Proxy

## Pre-requisite:

- Ubuntu 16.04+
- Install Docker using the below command:

```
curl -sSL https://get.docker.com/ | sh
```

- Install Docker Compose using the below command:

```
curl -L https://github.com/docker/compose/releases/download/1.22.0-rc2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

## Initialize Swarm Repository

```
docker swarm init --advertise-addr <vps-ip> --listen-addr <vps-ip>:2377
```

## Clone this Repository

```
git clone https://github.com/dockerworxinc/nginx-reverse-proxy
```

## Bringing up WordPress Stack

```
cd nginx-reverse-proxy/creusere.tld
docker stack deploy -c docker-compose.yml wp_stack_1
```

## Bringing up NextCloud Stack

```
cd nginx-reverse-proxy/nextcloud.tld
docker stack deploy -c docker-compose.yml nxt_stack_1
```

## Bringing up Nginx Stack

```
cd nginx-reverse-proxy/nginx-proxy
docker stack deploy -c docker-compose.yml proxy_stack
```

## Scale up the containers using swarm for High Availability

```
docker service scale wp_stack_1=5
docker service scale nxt_stack_1=5
docker service scale proxy_stack=5
```

# A Bonus

## How to start Swarm Visualizer

```
docker service create   --name=viz   --publish=8084:8080/tcp   --constraint=node.role==manager   --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock   dockersamples/visualizer
```

##  How to start Portainer

 - user - admin, password - portainer
```
docker service create --name portainer --publish 9000:9000 --replicas=1 --constraint 'node.role == manager' --mount type=bind,src=//var/run/docker.sock,dst=/var/run/docker.sock --mount type=volume,src=portainer_data,dst=/data portainer/portainer -H unix:///var/run/docker.sock
```
