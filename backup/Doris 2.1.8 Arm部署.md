[https://doris.apache.org/zh-CN/docs/3.x/gettingStarted/quick-start](https://doris.apache.org/zh-CN/docs/3.x/gettingStarted/quick-start#%E7%AC%AC-1-%E6%AD%A5%E4%B8%8B%E8%BD%BD-quick-start-%E8%84%9A%E6%9C%AC)

# **使用 Docker 快速部署**

自 Doris 2.1.8 版本后，可以使用 Docker 进行快速部署。

### **第 1 步：下载 Quick-Start 脚本**

[[下载脚本](https://doris.apache.org/files/start-doris.sh)](https://doris.apache.org/files/start-doris.sh) , 运行以下命令，给其赋有相应的执行权限。

```bash
chmod 755 start-doris.sh
```

### **第 2 步：启动集群**

运行脚本，启动集群，默认使用`2.1.9`版本，可通过 -v 参数指定启动版本，如：

```bash
bash start-doris.sh -v 2.1.8
```

### **第 3 步：使用 MySQL 客户端连接集群，并检查集群状态**

```sql
## 检查 FE 状态，确定 Join 与 Alive 列均为 true
mysql -uroot -P9030 -h127.0.0.1 -e 'SELECT `host`, `join`, `alive` FROM frontends()'
+-----------+------+-------+
| host      | join | alive |
+-----------+------+-------+
| 127.0.0.1 | true | true  |
+-----------+------+-------+

## 检查 BE 状态，确定 Alive 列为 true
mysql -uroot -P9030 -h127.0.0.1 -e 'SELECT `host`, `alive` FROM backends()'
+-----------+-------+
| host      | alive |
+-----------+-------+
| 127.0.0.1 |     1 |
+-----------+-------+

```

也可以使用数据库管理工具，账号root，密码为空