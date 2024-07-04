#### 背景: 
新搞了台服务器, 用来搭建种种服务, 这个时候就要搭建一个Dokcer环境; 但是目前的网络状况你们懂的, 无法直接访问docker.com, 遂尝试另一种搭建方案

首先阅读Docker官方文档
[Install from a package](https://docs.docker.com/engine/install/ubuntu/#install-from-a-package)
If you can't use Docker's apt repository to install Docker Engine, you can download the deb file for your release and install it manually. You need to download a new file each time you want to upgrade Docker Engine.
1. Go to https://download.docker.com/linux/ubuntu/dists/.

2. Select your Ubuntu version in the list.

3. Go to pool/stable/ and select the applicable architecture (amd64, armhf, arm64, or s390x).

4. Download the following deb files for the Docker Engine, CLI, containerd, and Docker Compose packages:
    - ontainerd.io_<version>_<arch>.deb
    - docker-ce_<version>_<arch>.deb
    - docker-ce-cli_<version>_<arch>.deb
    - docker-buildx-plugin_<version>_<arch>.deb
    - docker-compose-plugin_<version>_<arch>.deb
5. Install the .deb packages. Update the paths in the following example to where you downloaded the Docker packages.
```
sudo dpkg -i ./containerd.io_<version>_<arch>.deb \
  ./docker-ce_<version>_<arch>.deb \
  ./docker-ce-cli_<version>_<arch>.deb \
  ./docker-buildx-plugin_<version>_<arch>.deb \
  ./docker-compose-plugin_<version>_<arch>.deb
```
6. Verify that the Docker Engine installation is successful by running the hello-world image.
```
sudo service docker start
sudo docker run hello-world
```