# Docker 学习笔记


一个Docker学习的简单笔记。

## Docker是什么？

略。

## Docker安装

参考[docker.com](http://docs.docker.com)上的文档安装Docker。

## 将当前用户添加到Docker组

为了避免每次执行docker命令都需要输入`sudo`，可以将当前用户添加到docker组。

```bash
sudo gpasswd -a ${USER} docker
```



然后退出重新进入即可。

```bash
docker version //快速检查
```





## 运行第一个镜像

```bash
docker run debian echo "Hello World"
```



运行一个命令行容器

```bash
$ docker run -i -t debian /bin/bash

#root@99a15501bbaf:/# echo "Hello from Container-lan"
#Hello from Container-lan
#root@99a15501bbaf:/# exit
#exit
```





## 基本命令

```bash
# -h hostname 命名容器
docker run -h CONTAINER -i -t debian /bin/bash


# 开一个新的终端
docker ps //运行的容器
docker inspect gloomy_yalow(NAMES) //容器信息 
docker inspect gloomy_yalow | grep IPAddress
docker diff gloomy_yalow //容器的变化
docker logs gloomy_yalow //容器内发生的事情
docker ps -a //所有容器，包括停止的
docker rm gloomy_yalow //删除容器
docker rm -v $(docker ps -aq -f status=exited) //删除所有已经停止的容器
```





## 一个cowsay的例子

```bash
docker run -it --name cowsay --hostname cowsay debian bash
apt-get update
apt-get install -y cowsay fortune
/usr/games/fortune | /usr/games/cowsay 
exit
docker commit cowsay test/cowsayimage
docker run test/cowsayimage /usr/games/cowsay "Moo"
```





## 使用Dockerfiles构建镜像

```plain
mkdir cowsay
cd cowsay/
touch Dockerfile
# 文件内容
FROM debian:latest

RUN apt-get update && apt-get install -y cowsay fortune
####

docker build -t test/cowsay-dockerfile .

docker run test/cowsay-dockerfile /usr/games/cowsay "Moo"

# 添加下面到Dockerfile，以便自动执行
ENTRYPOINT ["/usr/games/cowsay"]
####

docker build -t test/cowsay-dockerfile .
docker run test/cowsay-dockerfile "Moo"

# 在主机cowsay目录下创建文件 entrypoint.sh
touch entrypoint.sh
# 文件内容
#!/bin/bash
if [ $# -eq 0 ]; then
    /usr/games/fortune | /usr/games/cowsay
else
    /usr/games/cowsay "$@"
fi
####
chmod +x entrypoint.sh 
#修改Dockerfile
FROM debian:latest

RUN apt-get update && apt-get install -y cowsay fortune
COPY entrypoint.sh /

ENTRYPOINT ["/entrypoint.sh"]
#####

docker build -t test/cowsay-dockerfile .
docker run test/cowsay-dockerfile
docker run test/cowsay-dockerfile Hello Moo
```





## 使用Redis官方镜像

```bash
docker pull redis
docker run --name myredis -d redis //-d 后台运行
docker run --rm -it --link myredis:redis redis /bin/bash //--rm 运行后删除容器，--link 连接两个容器 已存在容器名/在本容器中使用名

redis-cli -h redis -p 6379
redis:6379> ping
PONG
redis:6379> set "abc" 123
OK
redis:6379> get "abc"
"123"
redis:6379> exit
root@e78e3538b2c8:/data# exit
exit

docker stop myredis
docker rm -v myredis

docker rm $(docker ps -aq)
```





## 使用docker进行开发

我们使用python和flask构建一个website。

### Hello, World

构建一个返回“hello，world”的web server。

创建以下的目录结构

```plain
identidock/
|-- app
|   `-- identidock.py
`-- Dockerfile

1 directory, 2 files
```



编辑`identidock.py`

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!\n'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```



编辑`Dockerfile`

```plain
ROM python:latest

RUN pip install Flask==0.10.1
WORKDIR /app
COPY app /app
CMD ["python", "identidock.py"]
```



创建镜像并测试

```bash
docker build -t identidock .
docker run -d p 5000:5000 identidock
curl localhost:5000
```



但是如果每次我们修改代码都要重新运行构建镜像也是一件麻烦的事情，我们改进一下

```bash
docker stop $(docker ps -lq) //停止最后运行的container
docker run -d -p 5000:5000 -v "$(pwd)"/app:/app identidock //将主机app目录挂载到容器/app目录下
curl localhost:5000

//修改python文件，返回不同的字符串，再次测试
curl lcoalhost:5000
```


---

> 作者: [张景林](https://zhangjinglin.github.io/about/)  
> URL: https://zhangjinglin.github.io/2016/03/docker-in-action/  

