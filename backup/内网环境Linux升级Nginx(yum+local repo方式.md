具体实现方式: 
在能访问外网的机器上获取镜像 然后通过local repo的方式使用yum更新nginx

-> 先备份!(备份流程不写了

## 在可访问外网的机器上**同步官方仓库**到内网

如果你想要**定期**获取官方（或其他镜像）最新的 Nginx 软件包（而不只是一次性下载某个版本），可以通过类似 `reposync` 或 `rsync` 的方式将外网仓库**镜像**到内网服务器。

### 1. 在外网机器上同步仓库

1. **安装所需工具**（以 CentOS/RHEL 为例）：

   ```sh
   sudo yum install yum-utils createrepo
   ```

2. **创建一个用于存储镜像的目录**：

   ```sh
   mkdir -p /home/mirror/nginx
   ```

3. **使用 `reposync` 同步官方 Nginx 仓库**（以 CentOS 7 为例）：
   ! 这一步需要按照Nginx官方教程, 配置Nginx官方repo !
   ```sh
   reposync -r nginx-stable -p /home/mirror/nginx --download-metadata
   ```

   - `-r nginx-stable` 指定要同步的仓库名称（这要与你的 `nginx.repo` 中 `[nginx-stable]` 的名字对应）
   - `-p /home/mirror/nginx` 指定下载路径
   - `--download-metadata` 下载元数据

4. **生成仓库元数据**（有时 reposync 会自动生成，也可手动执行）：

   ```sh
   createrepo /home/mirror/nginx/nginx-stable/
   ```

### 2. 将仓库数据传输到内网

可以使用多种方式把同步好的 `/home/mirror/nginx` 目录打包或拷贝到内网服务器：

- 用硬盘/U 盘拷贝
- scp / rsync 内网跳板机
- 离线光盘 / DVD 烧录等

### 3. 在内网服务器上建本地仓库

1. **将镜像内容放到固定目录**，如 `/srv/repo/nginx`。

2. 创建（或更新）YUM 仓库元数据

   ```sh
   createrepo /srv/repo/nginx/nginx-stable/
   ```

3. **在各内网机器上配置本地仓库 `.repo` 文件**

   ```ini
   [nginx-stable-local]
   name=nginx stable local
   baseurl=file:///srv/repo/nginx/nginx-stable
   enabled=1
   gpgcheck=0
   ```

4. 执行nginx升级

   **执行 Nginx 升级**

   ```sh
   sudo yum update nginx
   ```

### 3. 验证

   ```sh
nginx -v
   ```

