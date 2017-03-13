Docker日常坑和技巧
===
## Mistakes

### 报错一：docker: Error response from daemon: Conflict. The name "/xxx" is already in use by container
#### 解决方案:
    $ docker ps -a                  #找到xxx的container_id
$ docker stop xxx(container_id) # 停止该容器
    $ docker rm xxx(container_id)   # 删除该容器

#### 执行过程(如果没有停止就删除容器，会报:you cannot remove a running container错误)：
    [root@VM_169_236_centos ~]# docker run -d -P --name web -v /src/webapp:/webapp training/webapp python app.py
    docker: Error response from daemon: Conflict. The name "/web" is already in use by container cbb2de760d33053ca3a3521757afb83fb3ebaf811171e549f1a65589f4523130. You have to remove (or rename) that container to be able to reuse that name..
    See 'docker run --help'.
    [root@VM_169_236_centos ~]# docker ps -a 
    CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS                   PORTS                     NAMES
    cbb2de760d33        training/webapp        "python app.py"          12 minutes ago      Up 12 minutes            0.0.0.0:32771->5000/tcp   web
    9bb014f2d683        training/postgres      "su postgres -c '/usr"   About an hour ago   Up About an hour         5432/tcp                  db
    000df6e54733        ubuntu                 "/bin/bash"              3 hours ago         Up 3 hours                                         networktest
    a2f0de3946eb        errorlife/sinatra:v2   "/bin/bash"              4 hours ago         Exited (0) 4 hours ago                             amazing_roentgen
    1254186ac498        09c56268cc94           "/bin/bash"              5 hours ago         Exited (0) 5 hours ago                             nauseous_pare
    5dbc52edff97        training/sinatra       "/bin/bash"              5 hours ago         Exited (0) 5 hours ago                             sick_shirley
    7d47fecc127b        training/webapp        "python app.py"          45 hours ago        Up 28 hours              0.0.0.0:80->5000/tcp      kickass_mirzakhani
    351461d3f808        ubuntu                 "/bin/"                  45 hours ago        Created                                            elegant_fermat
    [root@VM_169_236_centos ~]# docker rm cbb2de760d33
    Error response from daemon: You cannot remove a running container cbb2de760d33053ca3a3521757afb83fb3ebaf811171e549f1a65589f4523130. Stop the container before attempting removal or use -f
    [root@VM_169_236_centos ~]# docker stop cbb2de760d33
    cbb2de760d33
    [root@VM_169_236_centos ~]# docker rm cbb2de760d33
    cbb2de760d33

### 巨坑一：运行tomcat7/8时间非常长
#### 解决方案[英文版](http://stackoverflow.com/questions/26431922/tomcat7-starts-too-late-on-ubuntu-14-04-x64-digitalocean/26432537#26432537) [中文版](https://my.oschina.net/wangnian/blog/687914)：
* 在Tomcat环境中解决
可以通过配置JRE使用非阻塞的Entropy Source。
在`catalina.sh`中加入这么一行：`-Djava.security.egd=file:/dev/./urandom `即可。
加入后再启动Tomcat，整个启动耗时下降到Server startup in 2912 ms。

* 在JVM环境中解决
 打开`$JAVA_PATH/jre/lib/security/java.security`这个文件，找到下面的内容
```
securerandom.source=file:/dev/random
```
替换成
```
securerandom.source=file:/dev/./urandom
```

## Tricks

### 技巧一：强制批量删除none的image镜像(强迫症不能忍.jpg)

    docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker stop
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker rm
docker images|grep none|awk '{print $3 }'|xargs docker rmi

### 技巧二：docker images的用法

    https://yeasy.gitbooks.io/docker_practice/content/image/list.html

### 技巧三： 清理掉无关文件(Dockerfile)

    --auto-remove $buildDeps

很多人初学 Docker 制作出了很臃肿的镜像的原因之一，就是忘记了每一层构建的最后一定要清理掉无关文件。 

### 技巧四：批量删除容器/镜像
    docker rm $(docker ps -q -a) 一次性删除所有的容器
    
    docker rmi $(docker images -q) 一次性删除所有的镜像
    
### 技巧五：解决Docker 运行的容器时间不对的问题
启动容器时候加一下 `-v /etc/localtime:/etc/localtime`，容器里面就会跟host一个时区了
也可以在Dockerfile里面指定。

 
> 如果觉得我的文章对您有帮助，请随意打赏～

<img src="../../res/wxmoney.jpg" width = "372" height = "432" alt="图片名称" align=center />
