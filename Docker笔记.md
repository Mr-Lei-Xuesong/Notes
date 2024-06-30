# 安装Dokcer

* 步骤：

  1. 先卸载旧版本

     ```shell
     yum remove docker \
                 docker-client \
                 docker-client-latest \
                 docker-common \
                 docker-latest \
                 docker-latest-logrotate \
                 docker-logrotate \
                 docker-engine
     ```

  2. 需要的安装包

     ```shell
     yum install -y yum-utils device-mapper-persistent-data lvm2
     ```

  3. 设置镜像仓库(阿里云)

     ```shell
     yum-config-manager \
         --add-repo \
     	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
     ```

  4. 安装Docker引擎

     ```shell
     yum install docker-ce docker-ce-cli containerd.io
     ```

  5. 启动Docker

     ```shell
     systemctl start docker
     ```

  6. 查看是否安装成功

     ```shell
     docker version
     ```

  7. 开机自启

     ```shell
     systemctl enable docker
     ```

# 卸载Docker

* 步骤：

  1. 卸载Docker引擎

     ```shell
     yum remove docker-ce docker-ce-cli containerd.io
     ```

  2. 删除相关文件

     ```shell
     rm -rf /var/lib/docker
     rm -rf /var/lib/containerd
     ```

# 配置阿里云加速器

* 通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

  ```shell
  sudo mkdir -p /etc/docker
  sudo tee /etc/docker/daemon.json <<-'EOF'
  {
    "registry-mirrors": ["https://u6a448qs.mirror.aliyuncs.com"]
  }
  EOF
  sudo systemctl daemon-reload
  sudo systemctl restart docker
  ```

# 安装常用软件

## MySQL

1. 下载镜像文件

   ```shell
   docker pull mysql:5.7
   ```

2. 创建实例并启动

   ```shell
   docker run -p 3306:3306 --name mysql -v /home/mysql/log:/var/log/mysql -v /home/mysql/data:/var/lib/mysql -v /home/mysql/conf:/etc/mysql -e MYSQL_ROOT_PASSWORD=leixuesong -d mysql:5.7
   ```

   * `-p 3306:3306`：将容器的3306端口映射到主机的3306端口
   * `-v /mydata/mysql/log:/var/log/mysql`：将日志文件夹挂载到主机
   * `-v /mydata/mysql/data:/var/lib/mysql`：将数据文件夹挂载到主机
   * `-v /mydata/mysql/conf:/etc/mysql`：将配置文件夹挂载到主机
   * `-e MYSQL_ROOT_PASSWORD=密码`：初始化root用户的密码

3. MySQL的配置

   ```shell
   vim /home/mysql/conf/my.cnf
   ```

   ```shell
   [client]
   default-character-set=utf8
   
   [mysql]
   default-character-set=utf8
   
   [mysqld]
   init_connect='SET collation_connection=utf8_unicode_ci'
   init_connect='SET NAMES utf8'
   character-set-server=utf8
   collation-server=utf8_unicode_ci
   skip-character-set-client-handshake
   skip-name-resolve
   ```

4. 重启MySQL

   ```shell
   docker restart mysql
   ```

5. 自动启动

   ```shell
   docker update mysql --restart=always
   ```

## Redis

1. 下载镜像文件

   ```shell
   docker pull redis
   ```

2. 创建实例并启动

   ```shell
   mkdir -p /home/redis/conf
   touch /home/redis/conf/redis.conf
   
   docker run -p 6379:6379 --name redis -v /home/redis/data:/data -v /home/redis/conf/redis.conf:/etc/redis/redis.conf -d redis redis-server /etc/redis/redis.conf
   ```

3. Redis的配置

   ```shell
   vim /home/redis/conf/redis.conf
   ```

   ```shell
   # 持久化到硬盘
   appendonly yes
   ```

4. 重启Redis

   ```shell
   docker restart redis
   ```

5. 自动启动

   ```shell
   docker update redis --restart=always
   ```

## Elasticsearch

1. 下载镜像文件

   ```shell
   docekr pull elasticsearch:7.4.2
   ```

2. 创建实例并启动

   ```shell
   mkdir -p /mydata/elasticsearch/config
   mkdir -p /mydata/elasticsearch/data
   echo "http.host: 0.0.0.0" >> /mydata/elasticsearch/config/elasticsearch.yml
   chmod -R 777 /mydata/elasticsearch/
   docker run --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx128m" -v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v /mydata/elasticsearch/data:/usr/share/elasticsearch/data -v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins -d elasticsearch:7.4.2
   ```

## Kibana

1. 下载镜像文件

   ```shell
   docekr pull kibana:7.4.2
   ```

2. 创建实例并启动

   ```shell
   docker run --name kibana -e ELASTICSEARCH_HOSTS=http://ip地址:9200 -p 5601:5601 -d kibana:7.4.2
   ```

## Jenkins

1. 下载镜像文件

   ```shell
   docker pull jenkins/jenkins:2.385
   ```

2. 创建实例并启动

   ```shel
   chmod 777 /home/jenkins
   docker run -d -uroot -p 9999:8080 -p 50000:50000 -v /home/jenkins/jenkins_home:/var/jenkins_home -v /home/maven:/usr/local/maven -v /home/git/bin/git:/usr/local/git -v /etc/localtime:/etc/localtime --name jenkins jenkins/jenkins:2.385
   
   docker run -d -it -p 9999:8080 -p 50000:50000 -v /home/jenkins:/var/jenkins_home -u 0  -v  /home/maven:/usr/local/maven   -v /home/git/bin/git:/usr/local/git    -v /etc/localtime:/etc/localtime -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker -v /usr/local/shell:/usr/local/shell --name jenkins --restart=always jenkins/jenkins:2.344
   
   ```

3. 修改镜像下载地址

   ```shell
   vim hudson.model.UpdateCenter.xml
   
   <?xml version='1.1' encoding='UTF-8'?>
   <sites>
     <site>
       <id>default</id>
       <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
     </site>
   </sites>
   ```

4. 重启Jenkins

   ```shell
   docker restart jenkins
   ```

5. 查看首次登录密码

   ```shell
   docker logs jenkins
   ```

## Nginx

1. 下载镜像文件

   ```shell
   docker pull nginx
   ```

2. 创建挂载目录

   ```shel
   mkdir -p /home/nginx/conf
   mkdir -p /home/nginx/log
   mkdir -p /home/nginx/html
   ```

3. 启动容器并拷贝文件

   ```shell
   # 生成容器
   docker run --name nginx -p 80:80 -d nginx
   # 将容器nginx.conf文件复制到宿主机
   docker cp nginx:/etc/nginx/nginx.conf /home/nginx/conf/nginx.conf
   # 将容器conf.d文件夹下内容复制到宿主机
   docker cp nginx:/etc/nginx/conf.d /home/nginx/conf/conf.d
   # 将容器中的html文件夹复制到宿主机
   docker cp nginx:/usr/share/nginx/html /home/nginx/
   ```

4. 删除正在运行的nginx容器

   ```shell
   docker rm -f nginx
   ```

5. 重新运行

   ```shell
   docker run -p 80:80 --name nginx -v /home/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/conf/conf.d:/etc/nginx/conf.d -v /home/nginx/log:/var/log/nginx -v /home/nginx/html:/usr/share/nginx/html -d nginx
   ```

6. 自动重启

   ```shell
   docker update nginx --restart=always
   ```

## Minio

1. 下载镜像文件

   ```shell
   docker pull minio/minio
   ```

2. 创建挂载目录

   ```shell
   mkdir /home/minio/config
   mkdir /home/minio/data
   ```

3. 启动容器

   ```shell
   docker run -d -p 9000:9000 -p 9001:9001 --name minio -e "MINIO_ROOT_USER=admin" -e "MINIO_ROOT_PASSWORD=leixuesong" -v /home/minio/data:/data -v /home/minio/config:/root/.minio minio/minio server --console-address ":9001" --address ":9000" /data
   ```

4. 自动重启

   ```shell
   docker update minio --restart=always
   ```

## Nexus

1. 下载镜像文件

   ```shell
   docker pull sonatype/nexus3
   ```

2. 创建挂载目录

   ```shell
   mkdir /home/nexus/nexus-data
   chmod 777 /home/nexus/nexus-data
   ```

3. 启动容器

   ```shell
   docker run -d -p 8081:8081 --name nexus -v /home/nexus/nexus-data:/nexus-data sonatype/nexus3
   ```

4. 自动重启

   ```shell
   docker update nexus --restart=always
   ```

# Docker常用命令

## 帮助命令

```shell
docker version # 显示Docker的版本信息
docker info    # 显示Docker的系统信息
docker 命令 --help    # 帮助命令
```

## 镜像命令

1. ==查看本地所有的镜像==

   ```shell
   docker images [options]
   ```

   * [options]

     * `-a`：列出所有镜像

     * `-q`：只显示镜像的ID

   ```shell
   REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
   hello-world   latest    d1165f221234   3 months ago   13.3kB
   
   # REPOSITORY  镜像的仓库源
   # TAG		  镜像的标签
   # IMAGE ID    镜像的ID
   # CREATED     镜像的创建时间
   # SIZE        镜像的大小
   ```

2. ==搜索镜像==

   ```shell
   docker search 镜像名称 [options]
   ```

   * [options]
     * `-f`：过滤条件

3. ==下载镜像==

   ```
   docker pull 镜像名称[options]
   ```

   * [options]
     * `:版本`：选择下载的版本(默认为latest最新版)

4. ==删除镜像==

   ```shell
   docker rmi [options] ID/镜像名称           # 删除单个镜像
   docker rmi [options] 镜像ID 镜像ID 镜像ID   # 删除多个镜像
   docker rmi [options] $(docker images -aq) # 删除全部镜像
   ```

   * [options]
     * `-f`：强制删除

## 容器命令

1. ==新建容器并启动==

   ```shell
   docker run [options] 镜像名称
   ```

   * [options]
     * `--name='名称'`：容器名称，用来区分容器
     * `-d`：后台方式运行
       * **docker容器后台启动后，就必须要有一个前台进程，否则docker发现没有应用，就会立即停止容器**
     * `-it`：使用交互方式运行，进入容器查看内容
     * `-p`：指定容器的端口
       1. -p ip:主机端口:容器端口
       2. -p 主机端口:容器端口
       3. -p 容器端口
       4. 容器端口
     * `-v`：数据卷挂载
     * `-e`：环境配置

   ```shell
   # 测试启动并进入容器
   docker run -it centos /bin/bash
   # 退出
   exit
   ```

2. ==列出运行的容器==

   ```shell
   docker ps [options]
   ```

   * [options]
     * `-a`：列出所有运行的程序，包括以前运行过的
     * `-n=?`：显示?个容器
     * `-q`：只显示容器的ID

3. ==删除容器==

   ```shell
   docker rm 容器ID # 删除指定容器,不能删除正在运行的程序
   
   docker rm -f 容器ID # 强制删除指定容器
   
   docker rm -f $(docker ps -aq) # 删除所有容器
   ```

4. ==退出容器==

   ```shell
   exit # 停止容器并退出
   
   Ctrl+P+Q #不停止容器退出
   ```

5. ==启动、重启、停止、杀掉容器==

   ```shell
   docker start 容器ID       # 启动容器
   
   docker restart 容器ID     # 重启容器
   
   docker stop 容器ID        # 停止当前正在运行的容器
   
   docker kill 容器ID		# 强制停止当前容器
   ```

## 其他命令

1. ==查看日志==

   ```
   docker logs [options] 容器ID
   ```

   * [options]
     * `-tf`：显示日志

2. ==查看容器中进程信息==

   ```shell
   docker top 容器ID
   ```

3. ==查看镜像元数据==

   ```
   docker inspect 容器ID
   ```

4. ==进入当前正在运行的容器==

   * 进入容器后开启一个新的终端，可以在里面操作

     ```shell
     docker exec -it 容器ID /bin/bash
     ```

   * 进入容器正在执行的终端,不会启动新的端口

     ```shell
     docker attach 容器ID
     ```

5. ==从容器内拷贝文件到主机==

   ```shell
   docker cp 容器ID:容器内路径 目的主机路径
   ```
   
6. ==查看CPU状态==

   ```shell
   docker stats
   ```

# 可视化

## portainer

```shell
# 安装portainer
docker pull portainer/portainer
# 启动portaioner
docker run -d -p 8088:9000 -v "/var/run/docker.sock:/var/run/docker.sock" portainer/portainer
# 访问portainer
ip地址:8088
```

# 容器数据卷

> 我们在使用docker容器的时候，会产生一系列的数据文件，这些数据文件在我们关闭docker容器时是会消失的，但是其中产生的部分内容我们是希望能够把它给保存起来另作用途的，Docker将应用与运行环境打包成容器发布，我们希望在运行过程钟产生的部分数据是可以持久化的的，而且容器之间我们希望能够实现数据共享,==容器的持久化和同步操作，容器间也是可以数据共享==

## 通过命令行挂载

```
docker run -it -v  /宿主机绝对路径目录:/容器内目录  镜像名
```

## 匿名和具名挂载

1. ==匿名挂载==

   ```shell
   docker run -d -P -v 容器内路径
   ```

2. ==具名挂载==

   ```shell
   docker run -d -P -v 卷名:容器内路径
   ```

3. ==volume==

   ```shell
   docker volume [options]
   ```

   * [options]
     * `ls`：查看所有volume的情况
     * `inspect`：查看卷的详细情况
     * `rm`：删除卷

## 数据卷容器

> 容器间的数据共享

```shell
docker run -it --name 子容器名 --volumes-from 父容器名 容器ID
```

# DockerFile

## DockerFile、Docker镜像、Docker容器之间的关系

1. DockerFile，需要创建一个DockerFile,DockerFile定义了一个进程需要的一切东西。DockerFile涉及的内容包括执行代码或者是文件、环境、变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程等等
2. Docker镜像，在用DockerFile定义一个文件后，docker build时会产生一个Docker镜像，当运行Docker镜像时，会真正开始提供服务
3. Docker容器，容器是直接提供服务

## 基础知识

1. 每条保留字指令都必须大写字母且后面要跟随至少一个参数
2. 指令按照从上到下，顺序执行
3. 每条指令都会创建一个新的镜像层，并对镜像进行提交

## 执行流程

1. docker从基础镜像运行一个容器
2. 执行一条指令并对容器做出修改
3. 执行类似docker commit的操作提交一个新的镜像层
4. docker再基于刚提交的镜像运行一个新容器
5. 执行docekrfile中的下一条指令直到所有都执行完成

## 示例

1. ==构建docker==

   ```shell
   # 指令(大写) 参数
   FROM centos
   # 数据卷
   VOLUME ["/volume01","/volume02"]
   
   CMD echo"---finished---"
   
   CMD /bin/bash
   ```

   ```shell
   docker build -f 文件名 -t 镜像名:TAG .
   ```

## 保留字指令

>FROM

基础镜像，当前新镜像是基于哪个镜像

> MAINTAINER

镜像维护者的姓名和邮箱地址

> RUN

容器构建时需要运行的命令

> EXPOSE

当前容器对外暴露出的端口

> WORKDIR

指定在创建容器后，终端默认登录进来的工作目录

> ENV

用来在构建镜像过程中设置环境变量

> ADD

将宿主机目录下的文件拷贝进镜像，且ADD命令会自动处理URL和解压tar压缩包

> COPY

类似ADD，拷贝文件和目录到镜像中。但不会解压

> VOLUME

容器数据卷，用于数据保存和持久化工作

> CMD

指定一个容器启动时要运行的命令

**DockerFile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换**

> ENTRYPOINT

指定一个容器启动时要运行的命令

ENTRYPOINT和CMD一样都是在指定容器启动程序及参数，**但ENTRYPOINT是在命令后面拼接**

> ONBUILD

当构建一个被继承的Dockerfile时运行命令，父镜像在被子类继承后父镜像的onbuild被触发

## 实践

> centos基础上增加vim、net-tools

```shell
FROM centos:7

MAINTAINER 228389787@qq.com

ENV MYPATH /usr/local

WORKDIR $MYPATH

RUN yum -y install vim

RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH

CMD echo "---success---"

CMD /bin/bash
```

>CMD与ENTRYPOINT的区别

```shell
FROM centos:7

RUN yum install -y curl 

CMD ["curl","-s","http://ip.cn"]
```

```shell
FROM centos:7

RUN yum install -y curl 

ENTRYPOINT ["curl","-s","http://ip.cn"]
```

> ONBUILD

```SHELL
FROM centos:7

RUN yum install -y curl 

ENTRYPOINT ["curl","-s","http://ip.cn"]

ONBUILD RUN echo "father images onbuild---886-"
```

```SHELL
FROM father/centos

RUN yum install -y curl 

CMD ["curl","-s","http://ip.cn"]
```

### 安装Tomcat

```SHELL
FROM centos:7

MAINTAINER 228389787@qq.com

# 把宿主机当前上下文的c.txt拷贝到容器的/usr/local路径下并且更名
COPY c.txt /usr/local/continer.txt

# 把Java与tomcat添加到容器中并解压
ADD jdk-8u261-linux-x64.tar.gz /usr/local
ADD apache-tomcat-9.0.37.tar.gz /usr/local

# 安装vim编辑器
RUN yum -y install vim

# 设置登录点
ENV MYPATH /usr/local
WORKDIR $MYPATH

#配置Java与tomcat环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_261
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.37
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.37
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

#容器运行时监听的端口
EXPOSE 8080

# 启动时运行tomcat
CMD /usr/local/apache-tomcat-9.0.37/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.37/bin/logs/catalina.out 
```

# 本地镜像上传到阿里云

1. ==提交到本地仓库==

```shell
 docker commit -m="提交的描述信息" -a="作者" 容器ID 目标镜像名:[TAG]
```

2. ==登录阿里云镜像==

```shell
docker login --username=a雷雪松 registry.cn-hangzhou.aliyuncs.com
```

3. ==将镜像推送到Registry==

```shell
$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/leixuesong/mycentos:[镜像版本号]
$ docker push registry.cn-hangzhou.aliyuncs.com/leixuesong/mycentos:[镜像版本号]
```

# SpringBoot微服务打包Docker镜像

1. 构建springboot项目

2. 打包应用

3. 编写dockerfile

   ```shell
   FROM java:8
   
   COPY *.jar /app.jar
   
   EXPOSE 8080
   
   ENTRYPOINT ["java","-jar","/app.jar"]
   ```

4. 构建镜像

5. 发布运行





