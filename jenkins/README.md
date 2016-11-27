# Part 1 - Jenkins & Docker

In this example we will run Jenkins from a container, which will in turn spin
up containers on the host computer.


## Base from official Jenkins containers

The official Jenkins container can be found with a quick search of Docker hub.
We can base our image off of the Jenkins image. We will specify a version but
you could just as easily base off of latest.

```
FROM jenkins:2.19.3
```

## Place the jenkins user in the sudoers file

In order to do so, we will setup a dockerfile. The first step within this is
to create a Jenkins user that has sudo access on the host computer so that they
may run Docker commands.

Admittedly, this is a security vulnerability if run in a production setting.
This example is deliberately kept simple for the purpose of learning Docker.

```
USER root
RUN apt-get update \
      && apt-get install -y sudo \
      && rm -rf /var/lib/apt/lists/*
RUN echo "jenkins ALL=NOPASSWD: ALL" >> /etc/sudoers
```

## Build the dockerfile

Building the dockerfile we just wrote is straight forward. This will result in
a newly built local docker image. The following command assumes you are in the
same directory as the dockerfile.

```
docker build -t jenkinsdocker .
```

## Run the docker image

Once the docker run command is run, with luck we will have a local jenkins server that can
spawn local docker containers. This server will be available at
http://localhost:8080/ or <ip>:8080 if you are running this from a cloud VM.

```
docker run -d -v /var/run/docker.sock:/var/run/docker.sock \
              -v $(which docker):/usr/bin/docker -p 8080:8080 jenkinsdocker
```

## Navigate to Jenkins and setup the instance

Once you go to the Jenkins homepage available at http://localhost:8080/, you
will be prompted for a administrators password. This can be found in the logs
of the Jenkins container while it was setting up. Furthermore, examining the
logs is a great exercise as many future docker issues can be resolved this
way.

First retrieve the container id with docker ps

```
docker ps
```

Next, pass in the container id to docker logs and scan for the creation of an
admin password.

```
docker logs <container id>
```

This password will enable you to proceed with setting up Jenkins.

## Run a docker command from the Jenkins console

1. Open the Jenkins home page in a browser and click the “create new jobs” link.
2. Enter the item name (e.g. “docker-test”), select “Freestyle project” and
click OK.
3. On the configuration page, click “Add build step” then “Execute shell”.
4. In the command box enter “sudo docker run hello-world”
5. Click “Save”.
6. Click “Build Now”.

With luck, you have triggered a successful docker hello world. On your host
docker VM, run

```
docker ps -a
```

To verify that the Docker hello world image was indeed run on the host computer.

Next, practice using creating a persistent volume to share between Jenkins instances in [part 2](https://github.com/PeterLamar/docker-workshop/tree/master/sharevolume)
