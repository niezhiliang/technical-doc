### Docker私有仓库搭建及使用

准备：一台服务器（www.niezhiliang.com）,本地机器（我的是Mac）

上传镜像源代码：https://github.com/niezhiliang/springbootwebsocket

#### 1.下载docker私有仓库镜像

```
docker pull registry:2.6
```

#### 2.编写docker-compose.yml将镜像跑起来

```
version: '3'

services:
  sign-online-eureka:
     image: docker.io/registry:latest
     restart: always
     container_name: private-registry
     hostname: private-registry
     ports:
       - "5000:5000"
     volumes:
       - /opt/data/registry:/tmp/registry registry
       - /etc/timezone:/etc/timezone
       - /etc/localtime:/etc/localtime
```

### 3. 本地上传镜像到私有仓库

- 1.下载代码到本地

```
git clone https://github.com/niezhiliang/springbootwebsocket
```

- 2.进入项目根目录，将源代码生成镜像

```
	./buile_image.sh
```

- 3. 打一个tag为推到私有仓库做准备

```
docker tag suyu/springbootwebsocket:1.0 www.niezhiliang.com:5000/suyu/springbootwebsocker:1.0

//查看镜像
docker images 

//将镜像推动到私有仓库
docker push www.niezhiliang.com:5000/suyu/springbootwebsocker

```
	
`我们会看到控制台打印这条语句，大概意思就是一个是（私有仓库）http协议一个是（本地）https协议，两个协议不一致，导致推送失败。我们将本地也设置为http就能推成功`

- mac环境设置

![异常](https://github.com/niezhiliang/technical-doc/blob/master/imgs/register1.png)

修改docker的Daemon 将私有仓库加进去，就好了Mac的操作为 Preference ==> Daemon


![设置](https://github.com/niezhiliang/technical-doc/blob/master/imgs/register2.png)

- linux环境设置
```
在/etc/docker文件夹下面创建daemon.json文件
加入仓库地址
{
    "insecure-registries": ["www.niezhiliang.com:5000"]
}
多个仓库之间，用逗号隔开。

{
    "insecure-registries" : ["registry1:5000"], 
    "insecure-registries" : ["registry2:5000"]
}
```




```
//Mac重启docker,其他系统自己想办法（重启有点慢得等会）
killall Docker && open /Applications/Docker.app

//再次推送
docker push www.niezhiliang.com:5000/suyu/springbootwebsocker
```
	
![成功](https://github.com/niezhiliang/technical-doc/blob/master/imgs/register3.png)


 #### 4.测试是否推送成功
 - 删除构建之前的镜像	
 ```
 docker rmi [镜像id]
 ```
 - 从私有仓库下载镜像
 ```
 docker pull www.niezhiliang.com:5000/suyu/springbootwebsocker:1.0
 ```
 - 运行容器看看项目是否正常启动(可以看到项目可以正常启动)
 ```
 docker run www.niezhiliang.com:5000/suyu/springbootwebsocker:1.0 
 ```
 ![成功](https://github.com/niezhiliang/technical-doc/blob/master/imgs/register4.png)


