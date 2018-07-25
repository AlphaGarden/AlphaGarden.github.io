---
layout: single
title:  "Docker Notes"
header:
  overlay_color: "#000"
  overlay_filter: "0.1"
  overlay_image: /
  cta_label: "Learn stuff step by step"
  cta_url: "https://github.com/AlphaGarden"
  caption: "Photo credit: [**_Jiayong Mo_**](https://alphagarden.github.io)"
classes: wide
date:   2018-06-23 16:26:01 -0600

excerpt: "A personal notes for Docker learning"
categories: Docker
---


``` bash
docker run -ti bash encryption xxxx
```
Use this to run a command in a new container. It suits the situation where you do not have a container running, and you want to create one, start it and then run a process on it.
``` bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
This is for when you want to run a command in an existing container. This is better if you already have a container running and want to change it or obtain something from it.
``` bash
docker exec
```

#### Docker command line

Structure

```bash
docker [OPTIONS] COMMAND [ARG...]
```

#### RUN COMMAND

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```



#### Dockerfile Explanation example.

```dockerfile
# A Dockerfile must start with a 'FROM' instruction. The FROM instruction specifies the Base Image from which you are building. 

FROM xxxxxxxxxx/base:14.04.5

# escape=\(backslash) 'sets the character used to escape characters in a Dockerfile. Default is \ (backslash)'， Note that escape is not working in RUN command except at the end of a line.
 

#Kafka 1.0.0 Installation
RUN mkdir -p /usr/share/kafka && \
    curl -Ls 'http://mirror.reverse.net/pub/apache/kafka/1.1.0/kafka_2.11-1.1.0.tgz' | \
    tar --strip-components=1 -zx -C /usr/share/kafka/

# Environment variables can also be used in certain instructions as variable to be interpreted by the Dockerfie.
# ENV can be accessed by $variable_name or ${variable_name}
ENV KAFKA_HOME /usr/share/kafka
ENV PATH $PATH:$KAFKA_HOME/bin

RUN mkdir -p /var/log/kafka/
RUN chmod -R 777 /var/log/kafka/

COPY server.properties $KAFKA_HOME/config/server.properties.template
COPY log4j.properties $KAFKA_HOME/config/log4j.properties

#==add additional libs
RUN rm -f $KAFKA_HOME/libs/zookeeper-3*
COPY *.jar $KAFKA_HOME/libs/
#====

ENV DEBIAN_FRONTEND noninteractive
ENV INITRD No
ENV LANG en_US.UTF-8
ENV GOVERSION 1.6.2
ENV GOROOT /opt/go
ENV GOPATH /root/.go

RUN cd /opt && wget https://storage.googleapis.com/golang/go${GOVERSION}.linux-amd64.tar.gz && \
    tar zxf go${GOVERSION}.linux-amd64.tar.gz && rm go${GOVERSION}.linux-amd64.tar.gz && \
    ln -s /opt/go/bin/go /usr/bin/ && \
    mkdir $GOPATH

ENV ARCH=amd64
ENV VERSION=0.14.0

RUN mkdir -p /tmp/install /tmp/dist \
    && wget -O /tmp/install/node_exporter.tar.gz xxxxxxxx.tar.gz \
    && cd /tmp/install \
    && tar --strip-components=1 -xzf node_exporter.tar.gz \
    && mv node_exporter /bin/node_exporter \
    && rm -rf /tmp/install

# add application user
RUN /usr/sbin/groupadd kafka
RUN /usr/sbin/useradd --gid kafka --home-dir /home/kafka --create-home --shell /bin/bash kafka

COPY supervisord.conf /etc/supervisor

COPY bootstrap.sh /usr/bin
RUN chmod 755 /usr/bin/bootstrap.sh

ENTRYPOINT ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
```

#### FROM

The `FROM` instruction initializes a new build stage and sets the *Base Image* for subsequent instructions. 

The `ARG` is the only instruction that may precede `FROM` in the `Dockerfile`.

```dockerfile
FROM <image> [AS <name>]
FROM <image>[:<tag>] [AS <name>]
FROM <image>[:<digest>] [AS <name>]

# example for ARG
ARG CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD /code/run-app
```

#### RUN

The `RUN` instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the `Dockerfile`.

* `RUN <command>` (shell form, the command in a shell, which by default is `/bin/sh -c` on `Llinux` or `cmd /S /C` on Windows)
* `RUN ["executable", "param1", "param2"]`  (exec form)

Example for running (exec form)

```dockerfile
RUN ["/bin/bash","-c","echo hello"]
```

#### CMD

**The main purpose of a `CMD` is to provide defaults for an executable container.** There can only be one`CMD` instruction in a `Dockerfile`. If you list more than one `CMD` then only the last `CMD` will take effect.  

* `CMD ["executable", "param1", "param2"] ` (exec form, the preferred style)
* `CMD ["param1", "param2"]` (*used as default parameters to ENTRYPOINT, then we must use ENTRYPOINT as well*)
* `CMD command param1 param2`(shell form)

*When `CMD` used in the shell or exec form, the `CMD` instruction sets the command to be **executed** when **running** the image.*

#### EXPOSE

```dockerfile
EXPOSE <port> [<port>/<protocol>]
```

The `EXPOSE` instruction informs Docker that the container listens on the specific network ports at run time, but this instruction does not really publish the port but instead a documentation for the `docker run -P` instruction to map the exposed ports.

```dockerfile
EXPOSE 80/tcp
EXPOSE 80/upd
```

Regardless the `EXPOSE` setting, we can override them at runtime by using the `-p` 

```bash
docker run -p 80:80/tcp -p 80:80/udp  
```

#### ENV

```dockerfile
ENV <key> <value>
ENV <key>=<value> ...
```

**The `Env` instruction could allow the <value> to be used with <key> in the environment for all subsequent instruction in the build stage and can be replaced inline in many as well.**

 #### ADD

```dockerfile
ADD [--chown=<user>:<group>] <src>... <dest>
ADD [--chown=<user>:<group>] ["<src>",... "dst"](this form is required for paths containing whitespace)
```

The `ADD` instruction copies new files, directories or remote file URLs from <src> to the <dest>, where <dest> will be the **` the filesystem of the image at the path <dest>. `**

```dockerfile
ADD hom* /mydir/        # adds all files starting with "hom"
ADD hom?.txt /mydir/    # ? is replaced with any single character, e.g., "home.txt"
```

#### COPY

```dockerfile
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "dst"](this form is required for paths containing whitespace)
```

The `COPY` instruction copies new files, directories or remote file URLs from <src> to the <dest>, where <dest> will be the **` the filesystem of the container at the path <dest>. `**

`COPY` obeys the following rules:

* The `src` path must be inside the context of the build; we are not allowed to `COPY ../something/something`, because the first step of a `docker build` is to send the context directory (and subdirectories) to the docker daemon.
* If `<src>` is a directory, the entire contents(not including the directory itself) of the directory are copied, including filesystem metadata.

#### ENTRYPOINT

an `ENTRYPOINT` allows you to configure a  container that will run as an *`executable`*.(executable = an executable program)

```dockerfile
ENTRYPOINT ["executable", "param1", "param2"](execform, perferred)
ENTRYPOINT command param1 param2(shell form)
```

* Command line arguments to `docker run <image>` will be appended after all elements in an *exec* form `ENTRYPOINT`, and will override all elements specified using `CMD`.
* The *shell* form prevents any `CMD` or `run` command line arguments from being used, but has the disadvantage that your `ENTRYPOINT`will be run as a subcommand of `/bin/sh -c`

**Only the last `ENTRYPOINT` instruction in the Dockerfile will have an effect**

The table to illustrate the between `ENTRYPOINT` & `CMD `:

||No ENTRYPOINT|ENTRYPOINT shell_cmd shell-p1|ENTRYPOINT["exec_cmd","exec_p1"]|
|---|---|---|---|
|No CMD|error, not allowed|/bin/sh -c shell_cmd shell_p1|exec_cmd exec_p1|
|CMD ["exec_cmd","p1_cmd"]|exec_cmd p1_cmd|/bin/sh -c shell_cmd shell-p1|exec_cmd exec_p1 exec_cmd p1_cmd|
|CMD ["p1_cmd","p2_cmd"]|p1_cmd p2_cmd|/bin/sh -c shell_cmd shell_p1|exec_cmd exec_p1 p1_cmd p2_cmd|
|CMD exec_cmd p1_cmd|/bin/sh -c exec_cmd p1_cmd|/bin/sh -c shell_cmd shell_p1|exec_cmd exec_p1 exec_cmd p1_cmd|

#### VOLUME 

```dockerfile
VOLUME ["/data"]
```

The `VOLUME` instruction creates a mount point with the specified name and marks it as holding externally mounted volumes form native host or other containers.

#### USER

```dockerfile
USER <user>[:<group>]
USER <UID>[:<GID>]
```

The `USER` instruction sets the user name (or UID) and optionally the user group(or GID) for running the image and for any `RUN`, `CMD`, `ENTRYPOINT` that follow it in the `Dockerfile`

```dockerfile
USER admin:rootgroup
RUN cd /usr/
```

#### WORKDIR

```dockerfile
WORKDIR /path/to/workdir
```

The `WORKDIR` instruction sets the working directory for any `RUN`, `CMD`, `ENTRYPOINT`, and `ADD` instruction that follow it in the `Docker file`.

*If the directory doesn't exist, then the directory will be created.*

```dockerfile
ENV DIRTPATH /home/jiayong
WORKDIR ${DIRPATH}
RUN pwd
```

we would get the output `/home/jiayong`

### ARG

```dockerfile
ARG <name>[=<defalut value>]
```

The `ARG` instruction defines a variable that users can pass at build-time to the builder with the `docker build` command using the `--build-arg <varname>=<value>` flag.

#### SHELL

```dockerfile
SHELL ["executable","parameters"]
```

The `SHELL` instruction allows the default shell used for the *shell* form of command to be overwritten.

```dockerfile
# set up the default bash to /bin/sh -c 
SHELL ["/bin/sh", "-c"]
# SHELL can be called multiple times, and affect all the following lines.
```

#### Reference 

[Docker Offical Doc](https://docs.docker.com/engine/reference/builder/)