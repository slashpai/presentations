+++
title = "Docker in Action"
outputs = ["Reveal"]
[reveal_hugo]
theme = "night"
margin = 0.2
+++

## Docker in Action

Let's learn docker by handson

{{% note %}}

- Docker handson session

{{% /note %}}

---

## Let's get to know docker more by executing cli commands

---

## Docker Commands

---

| Command | Description | Example|
| --- | ----------- | ---------- |
| docker info | Display system-wide information | docker info |
| docker version | Show the Docker version information | docker version |
| docker pull | Pull an image or a repository from a registry| docker pull redis|
| docker images | List images| docker images |
| docker run | Run a command in a new container| docker run -d redis|

---

| Command | Description | Example|
| --- | ----------- |---------- |
| docker ps | List containers| docker ps |
| docker exec | Run a command in a running container| docker exec -it <id/name> sh |
| docker inspect| Return low-level information on Docker objects | docker inspect <object id/name>
| docker  logs | Fetch the logs of a container | docker logs <id/name> |

---

| Command | Description | Example|
| --- | ----------- |---------- |
| docker  stats | Display a live stream of container(s) resource usage statistics | docker stats |
| docker network | Manage networks | docker network ls |
| docker rename | Rename a container | docker rename \<old\> \<new name\> |

---

| Command | Description | Example|
| --- | ----------- |---------- |
| docker  stop | Stop one or more running containers| docker stop <id/name> |
| docker  rm | Remove one or more containers| docker rm <id/name> |
| docker  rmi | Remove one or more images| docker rmi \<image id> |

---

| Command | Description | Example|
| --- | ----------- |---------- |
| docker volume | Manage volumes | docker volume ls  |
| docker  build | Build an image from a Dockerfile| dockr build . -t myapp:v1 |

---

## Command Help

```go
docker [command] --help
```

### Example

```go
> docker exec --help

Usage:	docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

Run a command in a running container

Options:
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a container
  -e, --env list             Set environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format: <name|uid>[:<group|gid>])
  -w, --workdir string       Working directory inside the container
```

---

## More commands

https://docs.docker.com/engine/reference/commandline/docker/

---

## Docker Volumes Usecase

Jenkins

---

- Jenkins version updates
- Plugin updates
- Without losing configs/jobs you already have

---

## Docker run with volumes

```go
docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

- This will store the workspace in /var/jenkins_home

- All Jenkins data lives in there - including plugins and configuration

- This will automatically create a 'jenkins_home' docker volume on the host machine, that will survive the container stop/restart/deletion

---

## View docker volume created

```bash
docker volume ls
```

---

## Alpine Images

- Alpine Linux is a security-oriented, lightweight Linux distribution based on musl libc and busybox

- Alpine image is only 5MB and has access to a package repository 

- https://github.com/alpinelinux/docker-alpine

---

### Why Alpine Images?

- Docker images today are big

- Usually much larger than they need to be

- There are a lot of ways to make them smaller, but the Docker populace still jumps to the ubuntu base image for most projects

- The size savings over ubuntu and other bases are huge:

```bash
REPOSITORY  TAG     IMAGE ID      CREATED      SIZE
alpine      latest  961769676411  4 weeks ago  5.58MB
ubuntu      latest  2ca708c1c9cc  2 days ago   64.2MB
debian      latest  c2c03a296d23  9 days ago   114MB
centos      latest  67fa590cfc1c  4 weeks ago  202MB
```

---

### Alpine based images vs normal

```bash
ruby  2.6.6            39853018958e      13 days ago    840MB
ruby  2.6.6-alpine     d86341b50737      5 weeks ago    49.8MB
```

---

## Building Images

---

## Dockerfile

- {{% fragment %}} Docker can build images automatically by reading the instructions from a Dockerfile {{% /fragment %}}
- {{% fragment %}} A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image {{% /fragment %}}
- {{% fragment %}} Using docker build users can create an automated build that executes several command-line instructions in succession {{% /fragment %}}

---

## Example

```go
FROM ubuntu

RUN apt-get update
RUN apt-get install –y nginx
CMD [“echo”,”Image created”]
```

---

## Dockerfile Commands

| Command | Description |
| --- | ----------- |
| FROM | Specify base image|
| RUN | List containers|
| ENTRYPOINT | to configure a container that will run as an executable|
| CMD | provide defaults for an executing container |
| COPY| copies new files or directories |

---

| Command | Description |
| --- | ----------- |
| ENV | sets the environment variable |
| EXPOSE| informs Docker that the container listens on the specified network ports |
| USER |  sets the user |
| WORKDIR| sets the working directory|

---

## Lets build images

### From terminal/git bash

```bash
git clone https://github.com/slashpai/docker_k8s_training.git
cd docker_k8s_training
cd docker_apps
```

---

## Example 1

### Simple WebApp

cd simple_webapp

- {{% fragment %}} A simple ruby web app using Sinatra {{% /fragment %}}

- {{% fragment %}} Lets understand the app first {{% /fragment %}}

- {{% fragment %}} Lets build the docker app from it {{% /fragment %}}

- {{% fragment %}} docker build  . -t simple-webapp:v1 {{% /fragment %}}

---

### Run docker containers

- docker run -d -p 9000:4567 --name simple-webapp1 simple-webapp:v1

- docker run -d -p 9001:4567 --name simple-webapp1 --env APP_COLOR=yellow simple-webapp:v1

- docker run -d -p 9002:4567 --name simple-webapp2 --env APP_COLOR=orange simple-webapp:v1

- Go to http://localhost:9000, http://localhost:9001 and http://localhost:9002

---

## Understanding Dockerfile

```go
FROM ruby:2.6.6-alpine

WORKDIR /usr/src/app

ENV APP_COLOR="pink"

COPY Gemfile ./

RUN gem install bundler && bundle install

COPY . .

EXPOSE 4567

CMD [ "ruby", "app.rb" ]
```

---

## Example 2

### Ruby Calculator

cd calculator

- {{% fragment %}} A simple ruby calaculator to show how we can use docker to run adhoc tasks {{% /fragment %}}

- {{% fragment %}} Lets understand the app first {{% /fragment %}}

- {{% fragment %}} Lets build the docker app from it {{% /fragment %}}

- {{% fragment %}} docker build  . -t simple-calculator:v1 {{% /fragment %}}

- {{% fragment %}} docker run simple-calculator:v1 3 + 4 {{% /fragment %}}

---

## Review the container created

- {{% fragment %}}  docker ps {{% /fragment %}} 
- {{% fragment %}}  docker ps -a  {{% /fragment %}} 
- {{% fragment %}} Any reason why it doesn't show in running container list? {{% /fragment %}}
- {{% fragment %}} Noticed the ENTRYPOINT in Dockerfile? {{% /fragment %}}

---

## Understanding Dockerfile

```go
FROM ruby:2.6.6-alpine

WORKDIR /usr/src/app

COPY . .

ENTRYPOINT ["ruby", "calculator.rb"]
```

---

## .dockerignore file

- {{% fragment %}} Before the docker CLI sends the context to the docker daemon, it looks for a file named .dockerignore in the root directory of the context{{% /fragment %}}

- {{% fragment %}}  If this file exists, the CLI modifies the context to exclude files and directories that match patterns in it{{% /fragment %}}

- {{% fragment %}} This helps to avoid unnecessarily sending large or sensitive files and directories to the daemon and potentially adding them to images using ADD or COPY{{% /fragment %}}

---

## Example 3

### Quiz App

cd quiz_app

- {{% fragment %}} A simple quiz app using node.js {{% /fragment %}}

- {{% fragment %}} Noticed the .dockerignore file? {{% /fragment %}}

- {{% fragment %}} Lets build the docker app from it {{% /fragment %}}

- {{% fragment %}} docker build  . -t quiz-app:v1 {{% /fragment %}}

---

## Some task for you

- docker run -d -p 3500:3000 quiz-app:v1
- Go to http://localhost:3500
- Try the docker quiz 🤩

---

## Understanding Dockerfile

```go
FROM node:15.3.0-alpine3.10

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
COPY package*.json ./

RUN npm install

# Bundle app source
COPY . .

EXPOSE 3000
CMD [ "node", "index.js" ]
```

---

### CMD vs ENTRYPOINT

- {{% fragment %}} CMD is an instruction that is best to use if you need a default command which users can easily override{{% /fragment %}}

- {{% fragment %}} ENTRYPOINT is preferred when you want to define a container with a specific executable{{% /fragment %}}

- {{% fragment %}} You cannot override an ENTRYPOINT when starting a container unless you add the --entrypoint flag{{% /fragment %}}

- {{% fragment %}} Combine ENTRYPOINT with CMD if you need a container with a specified executable and a default parameter that can be modified easily {{% /fragment %}}

{{% note %}}

- If a Dockerfile has multiple CMDs, it only applies the instructions from the last one

- For example, when containerizing an application use ENTRYPOINT and CMD to set environment-specific variables.

{{% /note %}}

---

### Explore Dockerfiles of some of the official images available in docker hub

https://hub.docker.com/search?q=&type=image&image_filter=store%2Cofficial

---

## Docker Compose

---

- {{% fragment %}}  Compose is a tool for defining and running multi-container Docker applications {{% /fragment %}} 
- {{% fragment %}} With Compose, you use a YAML file to configure your application’s services {{% /fragment %}} 
- {{% fragment %}}  Then, with a single command, you create and start all the services from your configuration {{% /fragment %}} 

---

### Commands

docker-compose \<command\>

```go
  build              Build or rebuild services
  config             Validate and view the Compose file
  create             Create services
  down               Stop and remove containers, networks, images, and volumes
  exec               Execute a command in a running container
  help               Get help on a command
  images             List images
  logs               View output from containers
  ps                 List containers
  restart            Restart services
  rm                 Remove stopped containers
  start              Start services
  stop               Stop services
  up                 Create and start containers
  version            Show the Docker-Compose version information
```

---

## Examples

```go
version: "3.7"

services:
  prometheus:
    image: prom/prometheus:v2.19.0
    container_name: prom
    ports:
      - "9090:9090"
  grafana:
    image: grafana/grafana:7.0.3
    container_name: grafana
    ports:
      - "3000:3000"
```

---

### Let's try docker-compose

```bash
cd to docker_k8s_training/docker-compose/monitoring
docker-compose up
```

Load http://localhost:3000 and http://localhost:9090

```bash
cd to docker_k8s_training/docker-compose/consul_cluster
docker-compose up
```

Load http://localhost:8500

---

### Run in detached mode/background

```bash
docker-compose up -d
```

---

### Explore docker-compose commands

```bash
docker-compose ps
docker-compose images
docker-compose stop
docker-compose rm
```

---

## Read More

https://docs.docker.com/compose/gettingstarted/

---

## Docker Image Layers Review

- Compare images docker history <image:tag>

---

## Read More

https://docs.docker.com/storage/storagedriver/

---

## Clean Up Unused containers/images

```bash
docker stop
docker rm
```

```bash
docker images
docker rmi
```

---

### List all exited containers

```bash
docker ps -a -f status=exited
```

### Remove all exited containers

```bash
docker rm $(docker ps -a -f status=exited -q)
```

---

### Docker Images Cleanup

- Docker images consist of multiple layers. Dangling images are layers that have no relationship to any tagged images

- They no longer serve a purpose and consume disk space

- If you build an image without tagging it, the image will appear on the list of dangling images

    ```bash
    docker images -f dangling=true
    ```

- When you’re sure you want to delete them

    ```bash
    docker image prune
    ```

---

- To remove specific image

  - You would require to first stop and remove container using the image before doing this

    ```bash
    docker rmi <image id/name:tag>
    ```

---

## Read More

https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes

---

## Training Summary

- Docker commands

- Building docker applications and run it

- Using Docker compose

---

## What Next?

- {{% fragment %}} Want to learn something new without the pain of installing and messing up your machine? {{% /fragment %}}

- {{% fragment %}}You know the answer how to solve that now 🤩 {{% /fragment %}}

---

{{< slide content="common.thankyou" >}}
