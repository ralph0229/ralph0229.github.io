# Nacos ARM 环境部署笔记（Docker + MySQL）

> 适用场景：
>
> * ARM 架构服务器（Apple Silicon / ARM 云服务器）
> * Docker 部署 Nacos
> * 使用外部 MySQL
> * 版本：2.2.3 / 2.3.x

---

## 一、环境说明

* 架构：`linux/arm64`
* 部署方式：Docker（standalone）
* Nacos 版本：

  * 新版本：2.3.x
  * 旧版本：2.2.3
* 数据库：外部 MySQL

---

## 二、拉取官方 Docker 配置

```bash
git clone https://github.com/nacos-group/nacos-docker.git
cd nacos-docker/example
```

官方命令：

```bash
./mysql-init.sh && docker-compose -f standalone-mysql.yaml up
```

⚠️ **问题说明**：
官方 `standalone-mysql.yaml` 会默认启动一个 MySQL 容器，但实际环境中已有 MySQL，因此需要调整配置。

---

## 三、修改 MySQL 配置（外部数据库）

### 1️⃣ 修改文件

使用文件：

```text
nacos-standalone-mysql.env
```

---

### 2️⃣ 修改为外部 MySQL 配置

```properties
PREFER_HOST_MODE=hostname
MODE=standalone
SPRING_DATASOURCE_PLATFORM=mysql

MYSQL_SERVICE_HOST=你的数据库HOST
MYSQL_SERVICE_DB_NAME=nacos_devtest
MYSQL_SERVICE_PORT=你的数据库端口
MYSQL_SERVICE_USER=你的数据库用户名
MYSQL_SERVICE_PASSWORD=你的数据库密码

MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true

NACOS_AUTH_IDENTITY_KEY=2222
NACOS_AUTH_IDENTITY_VALUE=2xxx
NACOS_AUTH_TOKEN=VGhpc0lzTXlDdXN0b21TZWNyZXRLZXkwMTIzNDU2Nzg=
```

---

## 四、初始化 Nacos 数据库

### 1️⃣ 初始化脚本来源

初始化 SQL 来自官方仓库：

```text
https://raw.githubusercontent.com/alibaba/nacos/{version}/distribution/conf/mysql-schema.sql
```

---

### 2️⃣ 常用版本 SQL 地址

#### Nacos 2.3.x

```text
https://raw.githubusercontent.com/alibaba/nacos/2.3.x/distribution/conf/mysql-schema.sql
```

#### Nacos 2.2.3

```text
https://raw.githubusercontent.com/alibaba/nacos/2.2.3/distribution/conf/mysql-schema.sql
```

将 SQL **手动导入 MySQL**。

---

## 五、启动 Nacos 容器

```bash
docker-compose -f standalone-mysql.yaml up -d
```

---

## 六、首次登录 & 初始化密码


**Nacos 首次访问生成初始密码页面**

<img width="1920" height="962" alt="Image" src="https://github.com/user-attachments/assets/42a5748a-b4a5-4bb8-a19a-95f6dc766efc" />

访问地址：

```text
http://<服务器IP>:8848/nacos
```

说明：

* 用户名：`nacos`
* 首次登录会生成随机密码（务必保存）

---


**Nacos 控制台首页（登录成功）**

<img width="1920" height="962" alt="Image" src="https://github.com/user-attachments/assets/7bce34b6-26bf-425b-bd6e-cd2272c701a3" />

---

## 七、部署旧版本 Nacos（2.2.3）

<img width="1920" height="1006" alt="Image" src="https://github.com/user-attachments/assets/87ac0788-4d55-4b5a-8bbf-a430e4a8f89c" />

**Docker Hub 查看 nacos/nacos-server v2.2.3（ARM 架构）**

---

### MySQL 初始化脚本

```text
https://raw.githubusercontent.com/alibaba/nacos/2.2.3/distribution/conf/mysql-schema.sql
```

---

## 八、开启 Nacos 鉴权

官方文档：
👉 [https://nacos.io/docs/next/manual/admin/auth/](https://nacos.io/docs/next/manual/admin/auth/)

### 修改配置文件

在 `nacos-standalone-mysql.env` 中 **新增一行**：

```properties
NACOS_AUTH_ENABLE=true
```

---

## 九、命名空间管理

**命名空间列表（public / 自定义命名空间）**
<img width="1920" height="889" alt="Image" src="https://github.com/user-attachments/assets/1d4b332c-71d1-469a-b5f9-7b0082f938eb" />
说明：

* 推荐：`dev / test / prod` 分命名空间
* 旧项目命名空间：`bnd-csdn`

---

## 十、配置管理（导入 / 导出）

**配置列表页面（application.yml / application-dev.yml 等）**

---

**配置导出 / 导入按钮**

<img width="1920" height="889" alt="Image" src="https://github.com/user-attachments/assets/c30d1bb9-35e5-4c0b-9386-e54a18677349" />

---

## 十一、服务启动成功验证

**Spring Boot 启动日志（成功注册 & 订阅配置）**

<img width="3188" height="2032" alt="Image" src="https://github.com/user-attachments/assets/f1b87a0a-ff4c-4512-81df-73f6e6a271ca" />

日志关键点：

* 成功连接 Nacos
* 成功订阅配置
* 应用正常启动

---

## 十二、总结

✅ ARM 架构下 Nacos Docker 部署成功
✅ 使用外部 MySQL
✅ 兼容 2.2.3 / 2.3.x
✅ 支持鉴权、命名空间、配置迁移

---