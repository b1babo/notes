---
id: 9s0k02hxv78c6mijo6sfbhi
title: Docker
desc: ''
updated: 1669678839211
created: 1669248714966
---


## docker install 


```bash
sudo pacman -Ss docker 
```

 
## docker start

启动

```bash
sudo systemctl  start docker
```

把用户加入docker用户组，无需每次输入sudo

```bash
sudo usermod -aG docker $USER
# 需要 logout 重新登录
```

## 设置国内源

新建或编辑daemon.json

```bash
vi /etc/docker/daemon.json
```

在daemon.json中编辑如下

```json
{

 "registry-mirrors": ["http://hub-mirror.c.163.com"]

}
```

国内源

```
网易
http://hub-mirror.c.163.com
Docker中国区官方镜像
https://registry.docker-cn.com
中国科技大学
https://docker.mirrors.ustc.edu.cn
```

## docker image

文件生成的容器实例，本身也是一个文件，称为容器文件。也就是说，一旦容器生成，就会同时存在两个文件： image 文件和容器文件。而且关闭容器并不会删除容器文件，只是容器停止运行而已。把应用程序及其依赖，打包在 image 文件里面。只有通过这个文件，才能生成 Docker 容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。

```bash
# 列出本机的所有 image 文件。
docker image ls
# 删除 image 文件
docker image rm [imageName]
#搜索 镜像
docker search [imageName]
# 拉取 image 文件
docker image pull [imageName]
```

## docker container 



#### 运行容器

```bash
docker container run ubuntu /bin/bash
# 参数
-i: 交互式操作。
-t: 终端。
-d: 后台运行，需要通过exec进入容器
-p: 如 8000:3000 容器的3000映射到本机的8000端口，默认是tcp，后面添加/udp绑定udp端口



#进入运行中的容器
sudo docker exec -it [containerId] /bin/bash  

# 列出本机正在运行的容器
docker container ls
docker ps -a 
# 列出本机所有容器，包括终止运行的容器
docker container ls --all
# 启动/停止 容器
docker start/stop [containerId]
# 终止 容器
docker container kill [containerId]
# 删除 容器
docker container rm [containerId]
```

### 镜像导出导入
```bash
### 将本地的镜像导出
docker save -o 导出的路径 镜像id/镜像名称:tag
#e.g.
docker save -o ./ubuntu.image ubuntu:16.04
 
### 加载本地的镜像文件
docker load -i 镜像文件
# e.g.
docker load -i ubuntu.image
```
## 参考
[Docker Compose 网络设置 - 掘金 (juejin.cn)](https://juejin.cn/post/6844903976534540296)