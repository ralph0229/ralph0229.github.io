
## 一、升级背景：
漏洞扫描显示linux服务器openssh有高危漏洞，需要进行修复。openssh是远程登录到linux服务器
的重要工具，要修复此漏洞需升级到最高版本（2025年1月中旬最新版本为9.9）。
漏洞代码:CVE-2023-38408,CVE-2008-3844,CVE-2008-3844,CVE-2019-16905,CVE-2021-41617,CVE-2020-15778


## 二、升级说明： 

 1、所有升级文件均从官网下载。 

 2、linux服务器系统为Centos7可适用此方案。 

 3、Centos7系统需要有yum源，方便解决依赖关系问题。如无法实现，则需上传同系统版本安装映 像文件，作为本地yum源。 

 4、安装过程受网络影响极大，一旦出现不可抗因素致网络断开，可能会导致升级中断，无法远程 连接，出现此情况需现场人员配合解决。 

 5、升级过程出现问题需现场人员根据回退方案进行恢复操作。


## 三、升级过程：
### 0. 安装并启用telnet
(1). 安装telnet
```sh
yum install telnet-server telnet xinet
```

(2). 修改配置文件
```sh
vi /etc/xinetd.d/telnet

disable= no       //将yes改为no
```
```sh
#让root可以登录
mv /etc/securetty /etc/securetty.bak
#重启服务
service xinetd restart
```

(3). 启动相应服务，然后使用telnet登录到服务器
```
systemctl start telnet.socket
systemctl start xinetd
chkconfig --list
```

### 1. 升级openssh版本到9.9p1
(1) 下载或上传安装包openssh-9.9p1.tar.gz到/root目录下
```
cd ~
```
(2) 关闭Selinux
```
setenforce 0 
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
```
(3) 安装依赖关系
```
yum install -y gcc pam-devel rpm-build wget zlib-devel openssl-devel net-tools
tar -xvf openssh-9.9p1.tar.gz
cd openssh-9.9p1
```

(4) 卸载旧版openssh
```
rpm -qa |grep openssh
```
<sub>PS:下一步骤为删除原有ssh，但不会影响现在进行的ssh会话，所以整个操作过程不要断开ssh会话，
否则无法再次连接，只能由现场人员进行回退后重新操作（推荐使用telnet进行升级）。</sub>

```
for i in $(rpm -qa |grep openssh);do rpm -e $i --nodeps;done
```

(5) 设置、编译、安装
```
./configure --prefix=/usr --sysconfdir=/etc/ssh --with-md5-passwords --with-pam --with-tcp-wrappers  --with-ssl-dir=/usr/local/ssl/lib --without-hardening
# 整体备份原来的ssh
mv /etc/ssh /etc/ssh.old
# 这一步我没执行(具体原因看问题总结
cp contrib/redhat/sshd.pam /etc/pam.d/sshd
#编译
make
#安装
make install
```

(6) 设置自启动
复制启动脚本到/etc/init.d
```
cp contrib/redhat/sshd.init /etc/init.d/sshd
chkconfig --add sshd
chkconfig sshd on
chkconfig --list|grep sshd
sed -i "32a PermitRootLogin yes" /etc/ssh/sshd_config
#重启ssh服务
service sshd restart
```

(7) 检查升级情况
```
ssh -V
```
输出版本号为“OpenSSH_9.9p1”，说明更新成功

### 2. 关闭telnet
```
# 关闭服务
systemctl stop telnet.socket
systemctl stop xinetd
# root可以登录
#mv /etc/securetty.bak /etc/securetty

# 卸载telnet
yum remove telnet-server  xinetd
```

---------------------------------------流程意料之外的问题汇总及解决方式记录--------------------------------------------------------
#### 1. 执行yum install telnet-server telnet xinet时, 找不到可用的xinetd包
```
yum clean all
yum makecache
yum search xinetd
```
更新了yum的缓存, 然后重新安装成功

#### 2. 执行vi /etc/xinetd.d/telnet后发现 telent文件为空, 故手动创建, 完整内容如下
```
service telnet
{
     flags           = REUSE
     socket_type     = stream
     wait            = no
     user            = root
     server          = /usr/sbin/in.telnetd
     log_on_failure  += USERID
     disable         = no
}
```

#### 4. 执行./configure --prefix=/usr --sysconfdir=/etc/ssh --with-md5-passwords --with-pam --with-tcp-wrappers  --with-ssl-dir=/usr/local/ssl/lib --without-hardening时报错checking OpenSSL library version... configure: error: OpenSSL >= 1.1.1 required (have "100020bf (OpenSSL 1.0.2k-fips  26 Jan 2017)")
这表明ssl版本太低, 要升级ssl... 由于篇幅问题, 放在下一篇文章记录


#### 5. 这条命令我并没有执行cp contrib/redhat/sshd.pam /etc/pam.d/sshd, 执行完使用root登录不进去, 具体原因没有探究
我的sshd文件原始内容如下
```
#%PAM-1.0
auth       required     pam_tally2.so onerr=fail deny=5 unlock_time=600 even_deny_root root_unlock_time=600
auth       required     pam_sepermit.so
auth       substack     password-auth
auth       include      postlogin
account    required     pam_tally2.so
account    required     pam_nologin.so
account    include      password-auth
password   include      password-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open env_params
session    optional     pam_keyinit.so force revoke
session    include      password-auth
session    include      postlogin
```
新的sshd文件如下
```
#%PAM-1.0
auth       required     pam_stack.so service=system-auth
account    required     pam_nologin.so
account    required     pam_stack.so service=system-auth
password   required     pam_stack.so service=system-auth
session    required     pam_stack.so service=system-auth
```

参考知乎回答 [校招中的“熟悉linux操作系统”一般是指达到什么程度](https://www.zhihu.com/question/517101428/answer/3041079679)

