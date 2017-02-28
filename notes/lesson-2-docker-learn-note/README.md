Docker学习笔记
===
## 0x01 Docker是什么？

### 如何通俗的理解Docker
作者：刘允鹏
链接：https://www.zhihu.com/question/28300645/answer/67707287
来源：知乎
著作权归作者所有，转载请联系作者获得授权。(已获授权)

Docker的思想来自于集装箱，集装箱解决了什么问题？在一艘大船上，可以把货物规整的摆放起来。并且各种各样的货物被集装箱标准化了，集装箱和集装箱之间不会互相影响。那么我就不需要专门运送水果的船和专门运送化学品的船了。只要这些货物在集装箱里封装的好好的，那我就可以用一艘大船把他们都运走。

docker就是类似的理念。现在都流行云计算了，云计算就好比大货轮。docker就是集装箱。

1.不同的应用程序可能会有不同的应用环境，比如.net开发的网站和php开发的网站依赖的软件就不一样，如果把他们依赖的软件都安装在一个服务器上就要调试很久，而且很麻烦，还会造成一些冲突。比如IIS和Apache访问端口冲突。这个时候你就要隔离.net开发的网站和php开发的网站。常规来讲，我们可以在服务器上创建不同的虚拟机在不同的虚拟机上放置不同的应用，但是虚拟机开销比较高。docker可以实现虚拟机隔离应用环境的功能，并且开销比虚拟机小，小就意味着省钱了。

2.你开发软件的时候用的是Ubuntu，但是运维管理的都是centos，运维在把你的软件从开发环境转移到生产环境的时候就会遇到一些Ubuntu转centos的问题，比如：有个特殊版本的数据库，只有Ubuntu支持，centos不支持，在转移的过程当中运维就得想办法解决这样的问题。这时候要是有docker你就可以把开发环境直接封装转移给运维，运维直接部署你给他的docker就可以了。而且部署速度快。

3.在服务器负载方面，如果你单独开一个虚拟机，那么虚拟机会占用空闲内存的，docker部署的话，这些内存就会利用起来。

总之docker就是集装箱原理。

## 0x02 安装([官网文档](https://docs.docker.com/engine/installation/))

### 系统环境：Centos 7.2 64位

### 官方文档要求Linux kernel至少3.10以上，且docker只能运行在64位的系统中。本来是打算用Centos 6.5 64位的，既然官方都建议用最新的了，那还是不折腾升级内核好了，执意要的请参考这篇[博文](http://blog.csdn.net/wuzhilon88/article/details/41621285/)。

    $ uname -r
    3.10.0-327.36.3.el7.x86_64

### 开始安装（yum）
1、使用sudo或root权限的用户登录到你的机器
2、确保现有的包是最新的

    $ sudo yum update -y -y表示yes

3、添加yum repo

    $ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
    [dockerrepo]
    name=Docker Repository
    baseurl=https://yum.dockerproject.org/repo/main/centos/7/
    enabled=1
    gpgcheck=1
    gpgkey=https://yum.dockerproject.org/gpg
    EOF

4、安装Docker软件包

    $ sudo yum install docker-engine -y

5、启用服务

    $ sudo systemctl enable docker.service

6、启动Docker守护进程

    $ sudo systemctl start docker

7、验证docker是通过在一个容器中运行测试图像正确安装的

     $ sudo docker run --rm hello-world
    
    # 默认情况下，每个容器在退出时，它的文件系统也会保存下来，这样一方面调试会方便些，因为你可以通过查看日志等方式来确定最终状态。另外一方面，你也可以保存容器所产生的数据。但是当你仅仅需要短暂的运行一个容器，并且这些数据不需要保存，你可能就希望Docker能在容器结束时自动清理其所产生的数据。
    
     Unable to find image 'hello-world:latest' locally
     latest: Pulling from library/hello-world
     c04b14da8d14: Pull complete
     Digest: sha256:0256e8a36e2070f7bf2d0b0763dbabdd67798512411de4cdcf9431a1feb60fd9
     Status: Downloaded newer image for hello-world:latest
    
     Hello from Docker!
     This message shows that your installation appears to be working correctly.
    
     To generate this message, Docker took the following steps:
      1. The Docker client contacted the Docker daemon.
      2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
      3. The Docker daemon created a new container from that image which runs the
         executable that produces the output you are currently reading.
      4. The Docker daemon streamed that output to the Docker client, which sent it
         to your terminal.
    
     To try something more ambitious, you can run an Ubuntu container with:
      $ docker run -it ubuntu bash
    
     Share images, automate workflows, and more with a free Docker Hub account:
      https://hub.docker.com
    
     For more examples and ideas, visit:
      https://docs.docker.com/engine/userguide/
      
### 脚本安装([入口](https://docs.docker.com/engine/installation/linux/centos/#install-with-the-script))

### 创建docker group

1、使用sudo或root权限的用户登录到你的机器。
2、创建docker group

    $ sudo groupadd docker

3、将你的用户名添加进docker group

    $ sudo usermod -aG docker your_username

4、注销后重新登录，确定用户拥有正确的权限。

5、确认docker group中的用户可以不用sudo命令就执行docker

    $ docker run --rm hello-world
    
### 在开机启动添加Docker守护进程
1、将Docker守护程序配置为在主机启动时自动启动

    $ sudo systemctl enable docker
    
## 0x03 基础命令 - 镜像管理

### 搜索镜像

使用`docker search`命令搜索远程仓库中共享的镜像。

    docker search TERM
    
例如搜索名称为mysql的镜像

    $ docker search mysql
    NAME                DESCRIPTION                 STARS  OFFICIAL  AUTOMATED
    mysql               MySQL is a widely used...   2763   [OK]
    mysql/mysql-server  Optimized MySQL Server...   178    [OK]


### 获取镜像

使用`docker pull`命令从网络上下载镜像。

    docker pull NAME[:TAG]
    
例如

    $ docker pull ubuntu
    Using default tag: latest
    latest: Pulling from library/ubuntu
    
    43db9dbdcb30: Downloading 1.494 MB/49.33 MB
    2dc64e8f8d4f: Download complete
    670a583e1b50: Download complete
    43db9dbdcb30: Pull complete
    2dc64e8f8d4f: Pull complete
    670a583e1b50: Pull complete
    183b0bfcd10e: Pull complete
    Digest: sha256:c6674c44c6439673bf56536c1a15916639c47ea04c3d6296c5df938add67b54b
    Status: Downloaded newer image for ubuntu:latest
    
不指定Tag的时候默认使用`:latest`，因此，上述命令实际上是`docker pull ubuntu:latest`。

### 查看镜像信息

使用`docker images`可以列出本地主机上已有的镜像列表。

    $ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    php                 latest              fe1a2c2228f4        2 days ago          364 MB
    mongo               latest              87bde25ffc68        2 days ago          326.7 MB
    ubuntu              latest              42118e3df429        9 days ago          124.8 MB
    redis               latest              4465e4bcad80        6 weeks ago         185.7 MB
    nginx               latest              0d409d33b27e        8 weeks ago         182.8 MB

还可以使用`docker inspect`命令查看单个镜像的详细信息

    $ docker inspect ubuntu
    [
        {
            "Id": "sha256:42118e3df429f09ca581a9deb3df274601930e428e452f7e4e9f1833c56a100a",
            "RepoTags": [
                "ubuntu:latest"
            ],
            "RepoDigests": [
                "ubuntu@sha256:c6674c44c6439673bf56536c1a15916639c47ea04c3d6296c5df938add67b54b"
            ],
              },
              ...
            "RootFS": {
                "Type": "layers",
                "Layers": [
                    "sha256:ea9f151abb7e06353e73172dad421235611d4f6d0560ec95db26e0dc240642c1",
                    "sha256:0185b3091e8ee299850b096aeb9693d7132f50622d20ea18f88b6a73e9a3309c",
                    "sha256:98305c1a8f5e5666d42b578043e3266f19e22512daa8c6b44c480b177f0bf006",
                    "sha256:9a39129ae0ac2fccf7814b8e29dde5002734c1699d4e9176061d66f5b1afc95c"
                ]
            }
        }
    ]

查看单项信息

    $ docker inspect -f {{".Config.Hostname"}} ubuntu
    827f45722fd6




### 删除镜像

使用`docker rmi`命令删除镜像。

    docker rmi IMAGE [IMAGE...]

其中IMAGE可以是镜像标签或者ID。

例如

    docker rmi ubuntu
    docker rmi php:7.0.1
    
### 创建镜像

创建镜像有三种方法：

- 基于已有镜像创建
- 基于本地模板导入
- 基于Dockerfile创建

#### 使用已有镜像创建

该方法主要使用`docker commit`命令。

    docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
    
包含以下主要选项

* -a --author=""，作者信息
* -m --message=""，提交信息
* -p --pause=true，提价时暂停容器运行

例如

    $ docker run -i -t ubuntu:latest /bin/bash
    root@5a86b68c4e6a:/# ls
    bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
    boot  etc  lib   media  opt  root  sbin  sys  usr
    root@5a86b68c4e6a:~# exit
    exit

    $ docker commit -m "create a new images" -a "mylxsw" 5a86b68c4e6a test-cont
    sha256:68f1237c24a744b05a934f1317ead38fc68061ade7981eaae158a2ba8da02a9b
    
    $ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    test-cont           latest              68f1237c24a7        3 seconds ago       124.8 MB
    php                 latest              fe1a2c2228f4        2 days ago          364 MB
    mongo               latest              87bde25ffc68        2 days ago          326.7 MB
    ubuntu              latest              42118e3df429        9 days ago          124.8 MB
    redis               latest              4465e4bcad80        6 weeks ago         185.7 MB
    nginx               latest              0d409d33b27e        8 weeks ago         182.8 MB

#### 基于本地模板导入

#### 基于Dockerfile创建

### 保存镜像文件

使用`docker save`命令保存镜像文件为本地文件。

    docker save -o ubuntu_latest.tar ubuntu:latest

### 载入镜像

使用`docker load`从本地文件再导入镜像压缩包到本地镜像仓库。

    docker load --input ubuntu_latest.tar
    692b4b3b88ff: Loading layer  2.56 kB/2.56 kB
    Loaded image: ubuntu:latest

### 上传镜像

上传镜像使用`docker push`命令。

    docker push NAME[:TAG]
    
默认上传镜像到DockerHub官方仓库。


## 0x04 基础命令 - 容器

### 创建容器

使用`docker create`命令创建一个容器，使用该命令创建的容器处于停止状态，需要使用`docker start`命令启动容器。

    docker create -it ubuntu:latest
    
例如：

    $ docker create -it ubuntu
    ddb96bff9de60765a5c10ef91c684e206866a095ec1dae2dbc66924b65d26602
    $ docker ps -a
    CONTAINER ID    IMAGE     COMMAND      CREATED         STATUS   PORTS    NAMES
    ddb96bff9de6    ubuntu   "/bin/bash"  10 seconds ago   Created           grave_shaw

也可以直接使用`docker run`命令创建并启动一个新的容器，等价于执行命令`docker create`和`docker start`。

    $ docker run ubuntu /bin/echo 'Hello world'
    Hello world

下面的命令让docker启动一个bash终端，允许用于与其进行交互

    $ docker run -i -t ubuntu /bin/bash
    root@d808be915a22:/#

> `-t`选项让docker分配一个伪终端并绑定到容器的标准输入上，`-i`则让容器的标准输入保持打开。

大多数情况下，我们希望容器以后台守护进程的形式运行，可以使用`-d`选项。

    $ docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1;done"
    1927a78fd6e6ca32dbf6a8efe86d83162dd974e6302d930a1766b44142f33804
    $ docker ps
    CONTAINER ID   IMAGE    COMMAND                  CREATED       STATUS    PORTS    NAMES
    1927a78fd6e6   ubuntu  "/bin/sh -c 'while tr"   3 seconds ago  Up 2 seconds prickly_mcclintock
    $ docker logs prickly_mcclintock
    hello world
    hello world
    hello world


### 终止容器

使用`docker stop`命令终止运行中的容器。

    $ docker stop prickly_mcclintock
    prickly_mcclintock

> 容器终止后可以使用`docker start`命令再次启动，也可以对运行的容器执行`docker restart`使其重启。

### 进入容器

使用`-d`选项启动容器后，容器会进入后台运行，用户无法查看容器中的信息。有时候需要进入容器进行操作，可以使用`docker attach`命令以及`docker exec`命令，`nsenter`等工具。

#### attach命令

`docker attach`命令是Docker自带的命令，使用的时候并不太方便，当多个窗口attach到同一个容器，所有窗口都会同步显示。某一个窗口堵塞，其它创建窗口就无法继续进行操作了。

    docker attach prickly_mcclintock
    
> 使用`docker attach`之后，如果使用`Ctrl+C`退出，则容器也会退出运行


#### exec命令

Docker提供了一个更加方便的工具exec，使用它可以直接在容器内运行命令。

    $ docker exec -it 9b3d /bin/bash
    root@9b3d40ebc289:/# ls
    bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
    boot  etc  lib   media  opt  root  sbin  sys  usr

### 删除容器

使用`docker rm`命令删除处于终止状态的容器。

    docker rm [OPTIONS] CONTAINER [CONTAINER...]
    
* -f --force=false 强制终止并删除一个运行中的容器
* -l --link=false 删除容器的连接，但是保留容器
* -v --volumes=false 删除容器挂载的数据卷

### 导出容器

使用导入容器命令可以实现将一个已经创建的容器导出到一个文件，一般可以用于容器的迁移。

    docker export CONTAINER

例如

    docker export 9b3d40 > container-migrate.tar
    
可以将导出的文件传输到其它机器上再进行导入。

### 导入容器

使用`docker import`命令导入容器作为镜像。

    $ cat container-migrate.tar| docker import - test/ubuntu
    sha256:7cae85635deaacdca3120196d9d068d6fc9980b73b2c904b80354a4ece3ceed5
    $ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    test/ubuntu         latest              7cae85635dea        4 seconds ago       109.9 MB

## 0x05 基础命令 - 数据管理
Docker 管理数据的两种主要的方法：

- 数据卷
- 数据卷容器
    
### 数据卷
数据卷是指在存在于一个或多个容器中的特定目录，此目录能够绕过Union File System提供一些用于持续存储或共享数据的特性。

- 数据卷可在容器之间共享或重用
- 数据卷中的更改可以直接生效
- 数据卷中的更改不会包含在镜像的更新中
- 数据卷的生命周期一直持续到没有容器使用它为止

### 之前提到一些初学者常犯的错误是把 Dockerfile 等同于 Shell 脚本来书写，这种错误的理解还可能会导致出现下面这样的错误：

    RUN cd /app
    RUN echo "hello" > world.txt

如果将这个 Dockerfile 进行构建镜像运行后，会发现找不到 /app/world.txt 文件，或者其内容不是 hello。原因其实很简单，在 Shell 中，连续两行是同一个进程执行环境，因此前一个命令修改的内存状态，会直接影响后一个命令；而在 Dockerfile 中，这两行 RUN 命令的执行环境根本不同，是两个完全不同的容器。这就是对 Dokerfile 构建分层存储的概念不了解所导致的错误。

## 利用数据卷容器来备份、恢复、迁移数据卷
可以利用数据卷对其中的数据进行进行备份、恢复和迁移。

### 备份
首先使用 `--volumes-from` 标记来创建一个加载 dbdata 容器卷的容器，并从主机挂载当前目录到容器的 /backup 目录。命令如下：

    $ sudo docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata
    
容器启动后，使用了 `tar` 命令来将 dbdata 卷备份为容器中 /backup/backup.tar 文件，也就是主机当前目录下的名为 `backup.tar` 的文件。

### 恢复
如果要恢复数据到一个容器，首先创建一个带有空数据卷的容器 dbdata2。

    $ sudo docker run -v /dbdata --name dbdata2 ubuntu /bin/bash
    
然后创建另一个容器，挂载 dbdata2 容器卷中的数据卷，并使用 `untar` 解压备份文件到挂载的容器卷中。

    $ sudo docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf
    /backup/backup.tar
    
为了查看/验证恢复的数据，可以再启动一个容器挂载同样的容器卷来查看

    $ sudo docker run --volumes-from dbdata2 busybox /bin/ls /dbdata
    
## 0x08 基础命令 - 网络配置

Docker目前提供了**映射容器端口到宿主主机**和**容器互联**的机制为容器提供网络服务。

### 端口映射

容器中运行了网络服务，我们可以通过`-P`或者`-p`参数指定端口映射。

* **-P** Docker会随机映射一个49000-49900之间的端口到容器内部的开放端口。
* **-p** 可以指定要映射的端口，格式为`ip:hostPort:containerPort`，可以多次使用`-p`指定多个映射的端口。

例如：

    docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py
    docker run -d -p 5000:5000 training/webapp python app.py
    docker run -d -p 5000:5000/udp training/webapp python app.py
    docker run -d -P training/webapp python app.py

> 使用`docker port [容器名称] 容器内端口` 查看端口映射绑定的地址。
    
    docker port nostalgic_morse 5000

### 容器互联通信

容器的链接（linking）系统是除了端口映射外的另一种容器应用之间交互的方式，它会在源和接收容器之间创建一个隧道，接收容器可以看到源容器指定的信息。

容器之间互联通过`--link`参数指定，格式为`--link name:alias`，其中name为要链接到的容器的名称，`alias`为这个连接的别名。

    docker run -d --name mysql-demo -e MYSQL_ROOT_PASSWORD=root mysql
    docker run --rm --name web --link mysql-demo:db ubuntu env
    
![-w567](https://oayrssjpa.qnssl.com/2016-08-13-14710787536308.jpg)

    
使用`docker ps`可以看到容器的连接。

Docker会在两个互联的容器之间创建一个安全的隧道，而且不用映射端口到宿主主机。Docker中通过两种方式为容器公开连接信息：

* **环境变量** 环境变量的方式采用连接别名的大写前缀开头，比如前面的例子中，所有以`DB_`开头的环境变量。
* **更新`/ect/hosts`文件** Docker也会添加host信息到父容器的`/etc/hosts`文件

查看`/etc/hosts`文件：
    
    docker run --rm --name web --link mysql-demo:db -i -t ubuntu /bin/bash

![-w615](https://oayrssjpa.qnssl.com/2016-08-13-14710792151456.jpg)


## 0x06 Dockerfile

Dockerfile是一个文本格式的配置文件，用户可以使用Dockerfile快速创建自定义的镜像。

### 基本结构

一般来说，Dockerfile分为四部分：

* 基础镜像信息
* 维护者信息
* 镜像操作指令
* 容器启动时执行的指令

### 指令

指令一般格式为`INSTRUCTION arguments`。

#### FROM

格式为`FROM <image>`。第一条指令必须为`FROM`指令，指定了基础镜像。

    FROM ubuntu:latest

#### MAINTAINER

格式为`MAINTAINER <name>`指定维护者信息。

    MAINTAINER mylxsw mylxsw@aicode.cc
    
#### RUN

格式为`RUN <command>`或者`RUN ["executable", "param1", "param2"...]`。每条指令将在当前镜像的基础上执行，并提交为新的镜像。

格式`RUN <command>`时将在shell终端中执行命令，也就是`/bin/sh -c`中执行，而`RUN ["executable", "param1", "param2"...]`则使用`exec`执行。

#### CMD

该命令提供容器启动时执行的命令，每个Dockerfile中只能与一条CMD命令，如果指定了多条，则只有最后一条会被执行。如果用户启动容器的时候指定了运行的命令，则会覆盖CMD指令。

格式支持三种：

* `CMD ["executable", "param1", "param2"] ` 使用exec执行
* `CMD command param1 param2`  使用`/bin/sh -c`执行
* `CMD ["param1", "param2"]` 提供给ENTRYPOINT的默认参数

#### EXPOSE

格式为`EXPOSE <port> [<port>...]`，该指令用于告诉Docker容器要暴露的端口号，供互联系统使用。
    
    EXPOSE 22 80 8443
    
上述指令暴露了22, 80, 8443端口供互联的系统使用，使用的时候可以指定`-P`或者`-p`参数进行端口映射。

#### ENV

格式为`ENV <key> <value>`。指定一个环境变量，会被后续的RUN指令使用，并且在容器运行时保持。

比如：

    ENV PG_MAJOR 9.3
    ENV PG_VERSION 9.35

#### ADD

格式为`ADD <src> <dest>`。该命令复制指定的`<src>`到`<dest>`，其中`<src`可以是Dockerfile所在目录的一个相对路径（文件或目录），也可以是网络上的资源路径或者是tar包。

> 如果`<src>`是tar包的话，会在dest位置**自动解压**为目录。

#### COPY

格式为`COPY <scr> <dest>`，复制本地主机的`<src>`到容器的`<dest>`，目标路径不存在则自动创建。使用本地目录为源目录时，推荐使用COPY。

> 注意，`ADD`命令和`COPY`命令基本上是一样的，只不过是`ADD`命令可以复制网络资源，同时会对压缩包进行自动解压，而`COPY`则是单纯的复制本地文件（目录）。

#### ENTRYPOINT

配置容器启动后执行的命令，并且不会被`docker run`提供的参数覆盖。每个Dockerfile中只能有一个ENTRYPOINT，当指定多个的时候，只有最后一个生效。

格式有两种：

* ENTRYPOINT ["executable", "param1", "param2"]
* ENTRYPOINT command param1 param2

#### VOLUME

格式为`VOLUME ["/data"]`，创建一个可以从本地主机或其它容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

#### USER

格式为`USER daemon`，用于指定运行容器时的用户名或者UID，后续的RUN命令也会使用指定的用户。

#### WORKDIR

格式为`WORKDIR /path/to/workdir`，用于为后续的RUN，CMD，ENTRYPOINT指令配置工作目录。

可以多次使用，如果后续指定的路径是相对路径，则会基于前面的路径。

    WORKDIR /a
    WORKDIR b
    RUN pwd
    
则最后得到的路径是`/a/b`。

#### ONBUILD

指定基于该镜像创建新的镜像时自动执行的命令。格式为`ONBUILD [INSTRUCTION]`。

### 创建镜像

编写完Dockerfile之后，就可以通过`docker build`命令构建一个镜像了。

> 可以通过`.dockerignore`指定忽略的文件和目录，类似于git中的`.gitignore`文件。

比如

    docker build -t build_repo/first_image /tmp/docker_builder
    

---

参考：

- [Docker技术入门与实战 杨保卫 戴王剑 曹亚伦编著](https://www.gitbook.com/book/yeasy/docker_practice/details)
- [Docker学习笔记](https://github.com/mylxsw/growing-up/blob/master/doc/Docker%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0-%E5%9F%BA%E7%A1%80%E5%91%BD%E4%BB%A4.md)
- [官方文档](https://docs.docker.com/)

> 如果觉得我的文章对您有帮助，请随意打赏～

<img src="../../res/wxmoney.jpg" width = "372" height = "432" alt="图片名称" align=center />