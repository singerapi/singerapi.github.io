## singerapi + docker

本次项目是基于上一次的项目 singerapi，实现应用容器化。

包括三个容器：

1. 前端服务容器（Nodejs）
2. 后端服务容器（Go）
3. 数据库容器（Mysql）



### 容器化实现

#### 后端容器化

1. 安装 docker

   [参考教程]: http://www.runoob.com/docker/centos-docker-install.html

2. 编写 Dockerfile

   ```
   FROM golang:latest
   
   MAINTAINER caiye "caiy29@mail2.sysu.edu.cn"
   
   RUN mkdir -p /go/src/github.com
   WORKDIR /go/src/github.com
   RUN go get github.com/singerapi/singer-server
   WORKDIR /go/src/github.com/singerapi/singer-server
   RUN go install
   
   EXPOSE 8080
   
   CMD ["singer-server"]
   ```

3. 构建并运行容器

   ```
   docker build -t singer-server .
   docker run -d -p 8080:8081 singer-server
   ```


#### Mysql 容器化

1. 安装 docker

2. 后端添加链接数据库的代码

   ```
    path := strings.Join([]string{userName, ":", password, "@tcp(", ip, ":", port, ")/", dbName, "?charset=utf8"}, "")
     DB, _ = sql.Open("mysql", path)
   ```

3. 编写 sql 文件，建库建表插入数据

4. 编写 Dockerfile 文件

   ```
   FROM mysql:5.7
   
   ENV MYSQL_ALLOW_EMPTY_PASSWORD yes
   
   COPY setup.sh /mysql/setup.sh
   COPY schema.sql /mysql/schema.sql
   COPY privileges.sql /mysql/privileges.sql
   
   CMD ["sh", "/mysql/setup.sh"]
   ```

5. 构建 docker 镜像，生成容器

   ```
   sudo docker build -t singer_sql
   sudo docker run -d --name mySinger_sql -it -p 3306:3306 singer_sql
   ```


#### 前端容器化

1. 配置 nginx.conf

   ```conf
   server {
           listen       80;
           server_name  localhost;
   
           location / {
               root   /usr/share/nginx/html;
               try_files $uri $uri/ @router;
               index  index.html index.htm;
           }
       }
   ```

2. 编写 Dockerfile

   ```
   FROM hub.c.163.com/library/nginx
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   RUN npm run build
   EXPOSE 8888
   ```

3. 构建并运行容器

   ```
   docker build -t client .
   docker run -p 88:80 client
   ```


#### 使用 docker-compose 部署应用

1. 编写 `docker-compose.yml`

   ```
   version: "3"
   services:
     mysql:
     	build:
     		context: ./mysqlDocker
     	container_name: mysingersql-service
     	networks:
     		- gowebnet
     		
     singer-server:
       build:
       	context: .
       ports:
         - "8080:4200"
       depends_on:
       	- mysql
       networks: 
         - gowebnet
         
   networks:
   	- gowebnet
   ```

2. 启动 docker

   ```
   docker-compose -f docker-compose.yml up -d singerapi
   ```






