## singerapi + docker

本次项目是基于上一次的项目 singerapi，实现应用容器化。

包括三个容器：

1. 前端服务容器（Nodejs）
2. 后端服务容器（Go）
3. 数据库容器（Mysql）



### 容器化实现

#### 前端容器化

1. 配置 nginx.conf
2. 编写 Dockerfile
3. 构建并运行容器

#### 后端容器化

1. 安装 docker

   可参考教程：http://www.runoob.com/docker/centos-docker-install.html

2. 编写 Dockerfile

3. 构建并运行容器

#### Mysql 容器化

1. 安装 docker
2. 后端添加链接数据库的代码
3. 编写 sql 文件，建库建表写数据
4. 编写 Dockerfile 文件
5. 构建 docker 镜像
6. 生成容器

#### 使用 docker-compose 部署应用

1. 编写 `docker-compose.yml`
2. 启动 docker





