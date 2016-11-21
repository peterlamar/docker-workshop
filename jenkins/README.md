# Jenkins & Docker

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

## Add plugins to jenkins

Here we will copy a jenkins.txt file with any desired plugins so that
our container can be configured and ready to go. It is also a good example of
when it is helpful to copy a file into a container.

```
USER jenkins
COPY plugins.txt /usr/share/jenkins/plugins.txt
RUN /usr/local/bin/plugins.sh /usr/share/jenkins/plugins.txt
```

### Create plugins.txt file

Make sure to create a plugins.txt file in the same directory as the dockerfile.
Included are some basic plugins, feel free to add in the future as needed.

```
$ cat plugins.txt
scm-api:latest
git-client:latest
git:latest
greenballs:latest
```
