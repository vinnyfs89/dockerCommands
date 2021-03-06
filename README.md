# Docker basic commands

## Docker

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package. By doing so, thanks to the container, the developer can rest assured that the application will run on any other Linux machine regardless of any customized settings that machine might have that could differ from the machine used for writing and testing the code.

### Commands

- List all containers `docker ps -a` or  `docker container ls -a`
- List available containers `docker ps` or `docker container ls`
- List available images `docker images` or `docker image ls`
- Remove container `docker rm`
- Remove docker imagem `docker rmi`
- Download, create a container using a local image or from default registry
  - Example 1 `docker run -p 4000:80 friendlyhello:latest`
  - Example 2 `docker run -v /var/www:/var/www -p 80:80 vinnyfs89/{image}`
- Start container `docker start`
- Stop container `docker stop`
- Attach container `docker attach (image_id) --sig-proxy=false`
  - CTRL + P + Q = exits terminal without container dropout
- Container logs `sudo docker logs -f <CONTAINER_ID>`
- Build Docker  images `docker build --tag image:tag .`

## Setting Http or Https Proxies

```console
# Dockerfile
# ...

ENV http_proxy host:port
ENV https_proxy host:port

# ...
```

## Setting custom DNS

- `touch /etc/docker/daemon.json`

```console
{
  "dns": ["your_dns_address", "8.8.8.8"]
}
```

- Restart docker service `sudo service docker restart`

## Docker-Compose

```console
  docker-compose up -d --build
  docker-compose stop
  docker-compose build
```

### Deploy your load-balanced stack

```console
docker stack deploy -c docker-compose.yml myAppName
```

You can scale the app by changing the replicas value in docker-compose.yml, saving the change, and re-running the docker stack deploy command.

### Take down the app

```console
docker stack rm myAppName
```

## Docker virtual machine

### Creating docker virtual machine

```console
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2
```

### List Docker Machines

```console
docker-machine ls
```

### Access Docker Machines

```console
docker-machine ssh myvm1
```

### Set Docker Machine as Manager

```console
docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.100"
```

Output:

```console

Swarm initialized: current node (0drnc6ulexaajs20z0cbxgm5u) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-824to0hbb6iepvwlopofkb2wmar3u6n47nu5ny42qpy3z36i2g-c9mbey12q7i20xanm9z749afl 192.168.22.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```

### Setting your machine as active

- Show myvm1 machine environments

```console
docker-machine env myvm1
```

```console
eval $(docker-machine env myvm1)
```

### Unsetting docker-machine shell variable settings

```console
  eval $(docker-machine env -u)
```

## Swarm

A swarm is a group of machines that are running Docker and joined into a cluster.

### Requirements

- docker-machine

```console
base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
```

- docker-machine bash completion (Confirm the version and save scripts to **/etc/bash_completion.d** or /usr/local/etc/bash_completion.d)

```console
base=https://raw.githubusercontent.com/docker/machine/v0.16.0
    for i in docker-machine-prompt.bash docker-machine-wrapper.bash docker-machine.bash
    do
      sudo wget "$base/contrib/completion/bash/${i}" -P /etc/bash_completion.d
    done
```

- run the command below in your bash:

```console
source /etc/bash_completion.d/docker-machine-prompt.bash
```

- add the line below to your **~/.bashrc**

```console
PS1='[\u@\h \W$(__docker_machine_ps1)]\$ '
```

### Enable swarm mode

```console
docker swarm init
```

### Join a swarm as worker

```console
docker-machine ssh myvm2 "docker swarm join \
--token SWMTKN-1-824to0hbb6iepvwlopofkb2wmar3u6n47nu5ny42qpy3z36i2g-c9mbey12q7i20xanm9z749afl \
192.168.22.100:2377"
```

### Viewing nodes in swarm from manager

```console
docker-machine ssh myvm1 "docker node ls"
```

### Creating directory in swarm from manager

```console
docker-machine ssh myvm1 "mkdir ./data"
```

### Leaving each node

```console
docker swarm leave

or

docker-machine ssh myvm2 "docker swarm leave"
```

### Take down the swarm

```console
docker swarm leave --force
```

## Deployment

Deploy your app on the swarm cluster

### Show service id for services of application

```console
docker service ls
```

### Show services of started docker-compose stack

```console
docker service ps myAppName_web
```

## Registry

### Login

```console
docker login registry.example.com
```

### Deploy to private registry

```console
docker login registry.example.com

docker stack deploy --with-registry-auth -c docker-compose.yml myAppName
```

### Accessing your cluster

```console
docker-machine ls


```

## More

More information [here](https://github.com/vinnyfs89/dockerCommands/blob/master/docker-160827013030.pdf) ou diretamente pelo [Slideshare](http://pt.slideshare.net/vinnyfs89/docker-essa-baleia-vai-te-conquistar?qid=aed7b752-f313-4515-badd-f3bf811c8a35&v=&b=&from_search=1)

## Reference

1. [https://docs.docker.com](https://docs.docker.com)
2. [https://github.com/docker](https://github.com/docker)
3. [https://opensource.com/resources/what-docker](https://opensource.com/resources/what-docker)
