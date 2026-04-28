#### 背景: 
新搞了台服务器, 用来搭建种种服务, 这个时候就要搭建一个Dokcer环境; 但是目前的网络状况你们懂的, 无法直接访问docker.com, 遂尝试另一种搭建方案

首先阅读Docker官方文档
[Install from a package](https://docs.docker.com/engine/install/ubuntu/#install-from-a-package)
If you can't use Docker's apt repository to install Docker Engine, you can download the deb file for your release and install it manually. You need to download a new file each time you want to upgrade Docker Engine.
1. Go to https://download.docker.com/linux/ubuntu/dists/.

3. Select your Ubuntu version in the list.

4. Go to pool/stable/ and select the applicable architecture (amd64, armhf, arm64, or s390x).

5. Download the following deb files for the Docker Engine, CLI, containerd, and Docker Compose packages:
    - ontainerd.io_<version>_<arch>.deb
    - docker-ce_<version>_<arch>.deb
    - docker-ce-cli_<version>_<arch>.deb
    - docker-buildx-plugin_<version>_<arch>.deb
    - docker-compose-plugin_<version>_<arch>.deb
    ```
    去下载符合你对应系统的docker deb安装包
    我这里使用 lsb_release -a 查看了一下Ubuntu的发型版本

    No LSB modules are available.
    Distributor ID: Ubuntu
    Description:    Ubuntu 22.04.3 LTS
    Release:        22.04
    Codename:       jammy

    遂最后的目录是 https://download.docker.com/linux/ubuntu/dists/jammy/pool/stable/amd64/
    ```

6. Install the .deb packages. Update the paths in the following example to where you downloaded the Docker packages.
```
# 将下载下来的deb包装包一起安装了
sudo dpkg -i ./containerd.io_<version>_<arch>.deb \
  ./docker-ce_<version>_<arch>.deb \
  ./docker-ce-cli_<version>_<arch>.deb \
  ./docker-buildx-plugin_<version>_<arch>.deb \
  ./docker-compose-plugin_<version>_<arch>.deb
```
7. Verify that the Docker Engine installation is successful by running the hello-world image.
```
# 这里是通过运行hello-world镜像来验证docker安装情况
sudo service docker start
sudo docker run hello-world
```

本应该到这里就结束了, 但是由于那个原因, 直接docker run hello-world会拉取镜像超时;
这里我选择用Cloudflare部署一个docker hub的代理服务, 来解决拉取镜像超时的问题, 请看下一篇文章.