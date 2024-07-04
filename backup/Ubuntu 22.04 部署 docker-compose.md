### 在部署完Docker之后, 为了方便的管理镜像的部署, 遂考虑部署docker-compose
首先还是阅读官方文档

[On Linux](https://docs.docker.com/compose/install/standalone/#on-linux)

```
Compose standalone

Note that Compose standalone uses the -compose syntax instead of the current standard syntax compose.
For example type docker-compose up when using Compose standalone, instead of docker compose up.

```
1. To download and install Compose standalone, run:

```
 curl -SL https://github.com/docker/compose/releases/download/v2.28.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```
2. Apply executable permissions to the standalone binary in the target path for the installation.

```
# 给docker-compose文件相应的权限
sudo chmod 777 /usr/local/bin/docker-compose
```
3. If the command docker-compose fails after installation, check your path. You can also create a symbolic link to /usr/bin or any other directory in your path. For example:


```
# 为了直接使用docker-compose命令 将其链接到/usr/bin/下
 sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
4. Test and execute compose commands using docker-compose.
```
docker-compose -v
Docker Compose version v2.28.1
```
