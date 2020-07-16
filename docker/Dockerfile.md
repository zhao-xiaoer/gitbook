# Dockerfile
Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明

## 定制一个镜像
以nginx为例  
```bash
# 创建一个Dockerfile文件
FROM nginx
RUN echo '这是一个本地构建的nginx镜像' > /usr/share/nginx/html/index.html
```
* FROM 定制的镜像都是基于 FROM 的镜像，这里的 nginx 就是定制需要的基础镜像
* RUN 用于执行后面跟着的命令行命令。有以下俩种格式
    > 1.shell  
    >> RUN <命令行命令>  
    >> <命令行命令> 等同于，在终端操作的 shell 命令  

    > 2.exec   
    >> RUN ["可执行文件", "参数1", "参数2"]  
    >> 例如：  
    >> RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline  
* 注意：Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大
  
```bash
FROM centos
RUN yum install wget  
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"  
RUN tar -xvf redis.tar.gz  

#以上执行会创建 3 层镜像。可简化为以下格式：
FROM centos
RUN yum install wget && wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" && tar -xvf redis.tar.gz
```

## 构建镜像
```bash
docker build -t nginx:test .
```

## 上下文路径
`docker build -t nginx:test .`中的 `.`即是上下文路径  
上下文路径，是指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包。  
* **解析**：由于 docker 的运行模式是 C/S。我们本机是 C，docker 引擎是 S。实际的构建过程是在 docker 引擎下完成的，所以这个时候无法用到我们本机的文件。这就需要把我们本机的指定目录下的文件一起打包提供给 docker 引擎使用。  
如果未说明最后一个参数，那么默认上下文路径就是 Dockerfile 所在的位置。
* **注意**：上下文路径下不要放无用的文件，因为会一起打包发送给 docker 引擎，如果文件过多会造成过程缓慢

## 指令详解

### COPY
复制指令，从上下文目录中复制文件或者目录到容器里指定路径
```bash
COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",...  "<目标路径>"]

# [--chown=<user>:<group>]：可选参数，用户改变复制到容器内文件的拥有者和属组。
# <源路径>：源文件或者源目录，这里可以是通配符表达式，其通配符规则要满足 Go 的 filepath.Match 规则。例如：
COPY hom* /mydir/
COPY hom?.txt /mydir/

# <目标路径>：容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动创建
```

### ADD
ADD 指令和 COPY 的使用格式一致（同样需求下，官方推荐使用 COPY）。功能也类似，不同之处如下：
* ADD 的优点：在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>。
* ADD 的缺点：在不解压的前提下，无法复制 tar 压缩文件。会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。具体是否使用，可以根据是否需要自动解压来决定

### CMD
类似于 RUN 指令，用于运行程序，但二者运行的时间点不同:
* CMD 在docker run 时运行
* RUN 是在 docker build 时运行

**作用**：为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束。CMD 指令指定的程序可被 docker run 命令行参数中指定要运行的程序所覆盖  
**注意**：如果 Dockerfile 中如果存在多个 CMD 指令，仅最后一个生效  
```bash
CMD <shell 命令> 
CMD ["<可执行文件或命令>","<param1>","<param2>",...] 
CMD ["<param1>","<param2>",...]  # 该写法是为 ENTRYPOINT 指令指定的程序提供默认参数
# 推荐使用第二种格式，执行过程比较明确。第一种格式实际上在运行的过程中也会自动转换成第二种格式运行，并且默认可执行文件是 sh
```

### ENTRYPOINT
类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。  
但是, 如果运行 docker run 时使用了 --entrypoint 选项，此选项的参数可当作要运行的程序覆盖 ENTRYPOINT 指令指定的程序。  
**优点**：在执行 docker run 的时候可以指定 ENTRYPOINT 运行所需的参数。  
**注意**：如果 Dockerfile 中如果存在多个 ENTRYPOINT 指令，仅最后一个生效   
**格式**: `ENTRYPOINT ["<executeable>","<param1>","<param2>",...]`  
可以搭配 CMD 命令使用：一般是变参才会使用 CMD ，这里的 CMD 等于是在给 ENTRYPOINT 传参  
```bash
FROM nginx

ENTRYPOINT ["nginx", "-c"] # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参 

#1.不传参运行
docker run nginx:test
#则容器会执行以下命令
nginx -c /etc/nginx/nginx.conf

#2.传参运行
docker run  nginx:test -c /etc/nginx/new.conf
#容器会执行以下命令,假设已存在new.conf命令
nginx -c /etc/nginx/new.conf
```

### ENV
设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量  
```bash
#格式 
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...

#如
ENV NODE_VERSION 7.2.0

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc"
```

### ARG
构建参数，与 ENV 作用一至。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量    
构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖  
**格式**: `ARG <参数名>[=<默认值>]`  

### VOLUME


### EXPOSE


### WORKDIR


### USER


### HEALTHCHECK


### ONBUILD
