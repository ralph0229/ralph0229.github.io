## 升级 OpenSSL 的基本思路

1. **下载新版 OpenSSL 源码**
   从 OpenSSL 官网 下载你所需要的 OpenSSL 版本（例如 1.1.1 系列或更新版本）。
  https://github.com/openssl/openssl/releases
2. **编译并安装到自定义目录**
   为了避免与系统自带的 OpenSSL 冲突，建议安装到例如 `/usr/local/ssl`（或 `/usr/local/openssl`）目录下。
3. **编译 OpenSSH 时指定新 OpenSSL 路径**
   在执行 OpenSSH 的 `./configure` 时，通过 `--with-ssl-dir` 参数指定新 OpenSSL 的安装目录，使得 OpenSSH 链接到新版 OpenSSL。

## 具体步骤

### 1. 下载新版 OpenSSL

以 OpenSSL 1.1.1u 为例，在终端中执行：

```
# 上传或下载openssl-1.1.1w.tar.gz到usr/local/src下
cd /usr/local/src
tar -zxvf openssl-1.1.1w.tar.gz
cd openssl-1.1.1w
```

### 2. 编译并安装 OpenSSL 到自定义目录

建议安装在 `/usr/local/ssl`（注意不要覆盖系统自带的 OpenSSL）：

```
./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib
make
make test

# make test报错 Parse errors: No plan found in TAP output 
```

#### 检查 Perl 依赖模块
```
yum install perl-Test-Simple

# 重新执行make test install
make test
make install

# 检查ssl版本
/usr/local/ssl/bin/openssl version

#报错
/usr/local/ssl/bin/openssl: error while loading shared libraries: libssl.so.1.1: cannot open shared object file: No such file or directory

#遇到动态链接器无法找到共享库的问题，我建议将 /usr/local/ssl/lib 添加到 LD_LIBRARY_PATH 环境变量中，或将其添加到 /etc/ld.so.conf.d/ 目录内，然后运行 ldconfig。
```

### 1. 更新系统动态链接库搜索路径

将 OpenSSL 库所在的目录（例如 `/usr/local/ssl/lib`）添加到系统的动态链接库搜索路径中。具体步骤如下：

1. **创建或编辑配置文件**
   在 `/etc/ld.so.conf.d/` 目录下新建一个配置文件（例如 `openssl.conf`），写入 OpenSSL 库所在的目录：

   ```sh
   echo "/usr/local/ssl/lib" > /etc/ld.so.conf.d/openssl.conf
   ```

2. **刷新链接器缓存**
   运行下面的命令来刷新动态链接库缓存：

   ```sh
   ldconfig
   ```

3. **测试 OpenSSL 版本**
   再次执行：

   ```sh
   /usr/local/ssl/bin/openssl version
   ```

   如果一切正常，就会输出正确的 OpenSSL 版本号。


