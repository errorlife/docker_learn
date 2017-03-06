# 创建一个swarm
#### 预计阅读时间：2分钟

当你完成教程安装步骤之后，就可以创建一个`swarm`。确保`Docker Engine`守护进程已经开始在主机上工作了。

1、打开终端或者用ssh连接到管理器机器，本教程使用名为manager1的机器。 如果使用`Docker Machine`，您可以使用以下命令通过SSH连接到它：

    $ docker-machine ssh manager1

2、运行下面的命令创建一个新的`swarm`

    $ docker swarm init --advertise-addr <MANAGER-IP>
    
注意：如果您使用`Docker for Mac`或`Docker for Windows`测试单节点`swarm`，只需运行没有参数的`docker swarm init`。 在这种情况下，不需要指定`--advertise-addr`。 要了解更多信息，请参阅有关如何[使用`Docker for Mac`或`Docker for Windows` 的`swarm`主题](https://docs.docker.com/engine/swarm/swarm-tutorial/#use-docker-for-mac-or-docker-for-windows)。

在本教程中，以下命令在manager1计算机上创建一个`swarm`：

    $ docker swarm init --advertise-addr 192.168.99.100
    Swarm initialized: current node (dxn1zf6l61qsb1josjja83ngz) is now a manager.
    
    若要向该`swarm`添加工作人员，请运行以下命令：
    
    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377
    
    若要向此群集添加管理器，请运行“`docker swarm join-token manager`”并按照说明操作。

`--advertise-addr`标志配置管理器节点公布其地址为`192.168.99.100`。 `swarm`中的其他节点必须能够通过IP地址访问管理器。 输出包括将新节点加入`swarm`的命令。节点将作为管理器或工作人员加入(`swarm`)，具体取决于`--token`标志的值。

3、运行`docker info`查看`swarm`的当前状态:

    $ docker info
    
    Containers: 2
    Running: 0
    Paused: 0
    Stopped: 2
      ...snip...
    Swarm: active
      NodeID: dxn1zf6l61qsb1josjja83ngz
      Is Manager: true
      Managers: 1
      Nodes: 1
      ...snip...
      
4、运行`docker node ls`命令查看关于节点的信息

    $ docker node ls
    
    ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
    dxn1zf6l61qsb1josjja83ngz `*`  manager1  Ready   Active        Leader
    
在节点ID旁边的`*`表示你当前正在连接这个节点。
`Docker`引擎`swarm`模式自动为机器主机名的节点命名。本教程涵盖后续步骤中的其他专栏。

## 下一步呢？

在教程的下一节中，我们将在集群中[添加两个节点](https://docs.docker.com/engine/swarm/swarm-tutorial/add-nodes/)。

> 如果觉得我的文章对您有帮助，请随意打赏～

<img src="../../../../res/wxmoney.jpg" width = "372" height = "432" alt="图片名称" align=center />