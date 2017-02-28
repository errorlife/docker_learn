# 开始使用swarm集群
#### 预计阅读时间：4分钟

本教程向您介绍`Docker`引擎`swarm`模式的特点。在开始之前，你可能需要熟悉一些[关键概念](https://docs.docker.com/engine/swarm/key-concepts/)。

本教程将包含如下几点：

* 在`swarm`模式下初始化一个`Docker`引擎的集群
* 添加节点到`swarm`
* 部署应用程序服务到`swarm`
* 当运行服务后开始进行`swarm`管理

本教程在终端窗口的命令行上输入`Docker`引擎`CLI`命令。你可以在联网的机器上安装`Docker`，并且在你选择的`shell`中舒适地运行命令。

如果你对Docker了解甚少，可以看看关于[Docker引擎](https://docs.docker.com/engine/)

## 设置

学习本教程所需要准备：

* 三台连通网络的机器
* `Docker`引擎1.12版本或者更新的版本
* 作为管理者机器的IP地址
* 三台主机之间的开放端口

### 三台连通网络的机器

本教程在`swarm`中使用三个网络主机作为节点。这些可以是在你的PC上的虚拟机，也可以是在数据中心，或者是云服务提供商。本教程使用下列机器名：

* manager1(管理者1)
* worker1(工人1)
* worker2(工人2)

    Note:你可以遵循许多教程步骤来测试单节点`swarm`，在这种情况下，你只需要一个主机。多节点命令将无法工作，但你可以初始化一个`swarm`，创建服务，并规模化它们。

### `Docker`引擎1.12版本或者更新的版本

本教程要求每一台主机都必须安装版本为1.12或者更新的版本的`Docker`引擎。请安装`Docker`引擎并确认每一台主机的Docker 引擎`daemon`(守护进程)都是开启的状态。你可以通过以下的步骤去获取最新版的`Docker`引擎：

* 在`Liunx`机器上安装`Docker`引擎
* 在`Mac`或者`Windows`下使用`Docker`

#### 在`Liunx`机器上安装`Docker`引擎

如果您使用的是基于Linux的物理计算机或云服务提供商提供的计算机作为主机，只需为你的平台按照[`Linux的安装说明`](https://docs.docker.com/engine/installation/)安装。当你安装完后，启动这三台机器。然后你就可以在Linux机器上测试单节点和多节点`swarm`场景了。

#### 在`Mac`或者`Windows`下使用`Docker`

另外，在一台计算机上安装最新的`Docker for Mac`或`Docker for Windows`应用程序。 您可以用此计算机测试单节点和多节点`swarm`，但是你将需要使用`Docker Machine`来测试多节点方案。

* 您可以使用`Docker for Mac`或`Windows`来测试`swarm`模式的单节点功能，包括初始化具有单个节点的`swarm`，创建服务和扩展服务。 `Docker"Moby"`在`Hyperkit(Mac)`或`Hyper-V(Windows)`将作为单个`swarm`节点。

* 目前，您不能使用`Docker for Mac`或`Windows`单独测试多节点`swarm`。 但是，您可以使用包含的`Docker Machine`版本创建`swarm`节点（请参阅`Docker Machine`和本地`VM`入门），然后按照教程了解所有多节点功能。 对于这种情况，您从`Docker for Mac`或`Docker for Windows`主机运行命令，但是Docker主机本身不参与群集（即，在我们的示例中它不会是`manager1`，`worker1`或`worker2`）。 创建节点后，您可以运行所有`swarm`命令，如`Mac`终端或`Windows PowerShell`所示，使用`Docker for Mac`或`Docker for Windows`运行。

### 作为管理者机器的IP地址

IP地址必须分配给主机操作系统可用的网络接口。`swarm`中的所有节点必须能够访问IP地址处的管理器。

因为其他节点在其`IP`地址上联系管理器节点，您应该使用固定的`IP`地址。

您可以在`Linux`或`macOS`上运行`ifconfig`以查看可用网络接口的列表。

如果你使用`Docker Machine`，你可以使用`docker-machine ls`或`docker-machine ip <MACHINE-NAME>`来获取管理器`IP`，例如`docker-machine ip manager1`。

本教程使用`manager1`：`192.168.99.100`。

### 三台主机之间的开放端口

下列端口必须可用。在某些系统中，这些端口默认打开。

* TCP端口2377用于集群管理通信
* TCP和UDP端口7946用于节点之间的通信
* UDP端口4789用于覆盖网络流量

如果您计划创建具有加密（--opt加密）的覆盖网络，您还需要确保允许ip协议50（ESP）流量。

## 下一步呢？
设置环境后，就可以创建一个[群集](https://docs.docker.com/engine/swarm/swarm-tutorial/create-swarm/)
> 如果觉得我的文章对您有帮助，请随意打赏～

<img src="../../../res/wxmoney.jpg" width = "372" height = "432" alt="图片名称" align=center />