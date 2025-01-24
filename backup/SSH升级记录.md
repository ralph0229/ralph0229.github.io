
一、升级背景：
漏洞扫描显示linux服务器openssh有高危漏洞，需要进行修复。openssh是远程登录到linux服务器
的重要工具，要修复此漏洞需升级到最高版本（2025年1月中旬最新版本为9.9）。
漏洞代码:CVE-2023-38408,CVE-2008-3844,CVE-2008-3844,CVE-2019-16905,CVE-2021-41617,CVE-2020-15778


二、升级说明： 

 1、所有升级文件均从官网下载。 

 2、linux服务器系统为Centos7可适用此方案。 

 3、Centos7系统需要有yum源，方便解决依赖关系问题。如无法实现，则需上传同系统版本安装映 像文件，作为本地yum源。 

 4、安装过程受网络影响极大，一旦出现不可抗因素致网络断开，可能会导致升级中断，无法远程 连接，出现此情况需现场人员配合解决。 

 5、升级过程出现问题需现场人员根据回退方案进行恢复操作。


三、升级过程：
0. 安装并启用telnet
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
#mv /etc/securetty /etc/securetty.bak
重启服务
service xinetd restart
```

(3). 启动相应服务，然后使用telnet登录到服务器
systemctl start telnet.socket
systemctl start xinetd
chkconfig --list

参考知乎回答 [校招中的“熟悉linux操作系统”一般是指达到什么程度](https://www.zhihu.com/question/517101428/answer/3041079679)

