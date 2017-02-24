如何使用Dockerfile构建Tomcat镜像并部署war
===
## 0x01 前记
### 我们都知道Docker构建一个镜像有两种方式：
    使用`docker commit`命令
    使用`Dockerfile`文件和`docker build`命令
### 那么这两种方式有何区别呢？
    相同点：底层实现上一样，最终都是通过`docker commit`生成镜像；
    不同点：通过`Dockerfile`生成镜像，能够实现镜像文档化，使得镜像可读且易维护；
    通过`docker build`生成镜像则相反，不能实现镜像文档化。
### 所以这篇文章采用Dockerfile来构建 :D

## 0x02准备工作
### 创建目录

    $ mkdir tomcat && cd tomcat # 创建一个目录来存放所需文件
$ touch Dockerfile && vi Dockerfile # 编写Dockefile
### Dockerfile （[Tomcat其他版本](https://hub.docker.com/_/tomcat/)）
    FROM tomcat:7-jre7 # tomcat7 + jre 7
    MAINTAINER "errorlife <steffan.fan@foxmail.com>" # 维护者
    CMD ["catalina.sh", "run"] #启动tomcat shell执行程序

## 0x03 构建镜像

### 构建镜像
    $ sudo docker build -t errorlife/tomcat . 
    # "-t",--tag,为当前镜像命名：errorlife/tomcat
    # ".", 指代当前目录
    
### 运行镜像
    $ sudo docker run -d -p 8080:8080 errorlife/tomcat
    # -d,--detach=false         指定容器运行于前台还是后台(-d即后台) 
    # "-p",--port,指定宿主机的8080端口绑定到镜像暴露的8080端口
    
### 运行结果

 1. 你可以使用`curl`命令确定是否正确部署成功
    
        $ curl http://docker.steffan.cn:8080/
 2. 使用浏览器访问http://ip:8080/
    
    ![tomcat](https://leanote.com/api/file/getImage?fileId=589d236eab6441287f0023c6)

当你见到熟悉的汤姆猫的时候，就证明你构建成功了。

### 部署war包

 1. 将[war包](https://pan.baidu.com/s/1gfMaY7x)上传到与`Dockerfile`文件同一目录(即tomcat)
    我使用的是[WinSCP](https://winscp.net/eng/download.php)，个人觉得好用到不行=。=
 2. 重新编写Dockerfile:

        $ vi Dockerfile

 3. Dockerfile:
    
        FROM tomcat:7-jre7
        MAINTAINER "errorlife <steffan.fan@foxmail.com>"
        ADD dockertest.war /usr/local/tomcat/webapps/ #将war包添加进webapps中
        CMD ["catalina.sh", "run"] #启动tomcat shell执行程序

 4. 运行结果
    ![hello,errorlife!](https://leanote.com/api/file/getImage?fileId=589d3782ab644125d600258e)

## 0x04 其他配置文件
### 应用场景
由于一些项目需要修改tomcat的`context.xml`,`setting.xml`,`tomcat-user.xml`等，还有一些`tomcat的lib包`，甚至是添加一些独有的`授权文件`，那么也应该在`dockerfile`中`add`进去，而不是直接放到`Linux`的文件管理系统中。

### Show一下我的Dockerfile

    FROM tomcat:7-jre7
    MAINTAINER "errorlife <steffan.fan@foxmail.com>"
    
    ADD settings.xml /usr/local/tomcat/conf/
    ADD tomcat-users.xml /usr/local/tomcat/conf/
    ADD config/license.xml 
    
    ADD classes12.jar /usr/local/tomcat/lib
    ADD ojdbc6.jar /usr/local/tomcat/lib
    ADD oracle-jdbc-10.2.jar /usr/local/tomcat/lib
    ADD context.xml /usr/local/tomcat/conf/
    ADD xxx.war /usr/local/tomcat/webapps/
    CMD ["catalina.sh", "run"]

### 结论：docker会将这些文件添加到tomcat镜像内部的路径

## 0x04 参考资料
[Docker自动部署Apache Tomcat](http://dockone.io/article/285)
[通过Dockerfile和通过commit生成镜像有什么区别？](http://www.aixchina.net/Question/172781)
网络素材

    