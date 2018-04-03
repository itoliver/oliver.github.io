---
layout:     post
title:      "服务器跳板机配置"
subtitle:   " \"jump service\""
date:       2018-04-03 18:00:00
author:     "Oliver"
tags:
    - linux自动化之路
---
<h4><strong style="font-family: Verdana;"><span style="font-family: 新宋体; font-size: 10pt;">实现环境</span></strong></h4>
<p style="margin-right: 36.0000pt; margin-left: 36.0000pt;"><span style="font-family: Verdana; font-size: 10pt;">Centos 7 64位</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">IP地址：172.17.11.186</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">Nginx：1.12.0</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">Mysql：5.7.18</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">PHP：7.1.4</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">yum源：aliyun源</span></p>
<h4><strong><span style="font-family: Verdana; font-size: 10pt;">首先下载好我们的需要的包</span></strong></h4>
<p><span style="font-family: Verdana; font-size: 10pt;">创建一个目录存放下载的软件</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost ~]</span><span style="font-family: Verdana; font-size: 10pt;"># mkdir /software</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost ~]</span><span style="font-family: Verdana; font-size: 10pt;"># cd /software/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">去它们各自的官网下载最新版，下载命令参考</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">wget https:</span><span style="font-family: Verdana; font-size: 10pt;">//cdn.mysql.com//Downloads/MySQL-5.7/mysql-boost-5.7.18.tar.gz</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">wget https:</span><span style="font-family: Verdana; font-size: 10pt;">//sourceforge.net/projects/pcre/files/pcre/8.40/pcre-8.40.tar.gz</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">wget http:</span><span style="font-family: Verdana; font-size: 10pt;">//nginx.org/download/nginx-1.12.0.tar.gz</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">wget http:</span><span style="font-family: Verdana; font-size: 10pt;">//hk1.php.net/distributions/php-7.1.4.tar.gz</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">这是需要的包</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># </span><span style="font-family: Verdana; font-size: 10pt;">l</span><span style="font-family: Verdana; font-size: 10pt;">s</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysql-boost-5.7.18.tar.g</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">nginx-1.12.0.tar.gz</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">php-7.1.4.tar.gz</span></p>
<p><strong><span style="font-family: Verdana; font-size: 10pt;">关闭系统限制</span></strong><strong><span style="font-family: Verdana; font-size: 10pt;">,可以换成iptables</span></strong><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">关闭系统防火墙</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;">#</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;yum install iptables-*</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl stop firewalld.service </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl disable firewalld.service </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;">#</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;systemctl star iptables</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;">#</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;systemctl enable iptables</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">关闭SElinux</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># setenforce 0</span></p>
<h4><strong><span style="font-family: Verdana; font-size: 10pt;">开始安装nginx</span></strong></h4>
<p><span style="font-family: Verdana; font-size: 10pt;">创建www账户 用来启动nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># useradd www -s /sbin/nologin </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">安装依赖的包</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># yum -y install pcre pcre-devel zlib zlib-devel gcc-c++ gcc openssl*</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">解压Nginx源码包</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># tar zxvf nginx-1.12.0.tar.gz </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">进入解压后的目录，对Nginx进行配置</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># cd nginx-1.12.0/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># ./configure --user=www --group=www</span><span style="font-family: Verdana; font-size: 10pt;">\</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">--prefix=/usr/local/nginx</span><span style="font-family: Verdana; font-size: 10pt;">\</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">--with-http_realip_module</span><span style="font-family: Verdana; font-size: 10pt;">\</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">--with-http_sub_module</span><span style="font-family: Verdana; font-size: 10pt;">\</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">--with-http_gzip_static_module</span><span style="font-family: Verdana; font-size: 10pt;">\</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">--with-http_stub_status_module </span><span style="font-family: Verdana; font-size: 10pt;">\</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">--with-pcre</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">编译和安装</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># make &amp;&amp; make install</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">启动Nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># /usr/local/nginx/sbin/nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">浏览器访问测试是否ok</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">关闭Nginx进程</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># killall nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># ps -ef|grep nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">nginx命令做软连接方便使用</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># ln -s /usr/local/nginx/sbin/nginx /sbin/nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">编写nginx启动脚本</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">cat &gt;&gt; </span><span style="font-family: Verdana; font-size: 10pt;">/usr/</span><span style="font-family: Verdana; font-size: 10pt;">lib/systemd/system/nginx.service &lt;&lt; EOF</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[Unit]</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Description=nginx - high performance web server</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Documentation=http:</span><span style="font-family: Verdana; font-size: 10pt;">//nginx.org/en/docs/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">After=network-online.target remote-fs.target nss-lookup.target</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">[Service]</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Type=forking</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">PIDFile=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/</span><span style="font-family: Verdana; font-size: 10pt;">local/nginx/logs/nginx.pid</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ExecStartPre=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/</span><span style="font-family: Verdana; font-size: 10pt;">sbin/nginx -t</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ExecStart=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/</span><span style="font-family: Verdana; font-size: 10pt;">sbin/nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ExecReload=</span><span style="font-family: Verdana; font-size: 10pt;">//usr/sbin/nginx -s reload</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ExecStop=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/</span><span style="font-family: Verdana; font-size: 10pt;">sbin/nginx -s stop</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">PrivateTmp=</span><span style="font-family: Verdana; font-size: 10pt;">true</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">[Install]</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">WantedBy=multi-user.target</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">EOF</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">修改完systemctl服务，需要重新加载下daemon</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl daemon-reload </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">用systemctl启动Nginx服务，并查看状态</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-1.12.0]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl start nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-1.12.0]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl status nginx</span></p>
<div><span style="font-size: 10pt; font-family: Verdana;">[root@localhost ~]# systemctl status nginx</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">● nginx.service - nginx - high performance web server</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp;Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp;Active: active (running) since Mon 2018-03-19 15:00:31 CST; 12min ago</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp; &nbsp;Docs: http://nginx.org/en/docs/</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp;Main PID: 4802 (nginx)</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp;CGroup: /system.slice/nginx.service</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;├─4802 nginx: master process /usr/sbin/nginx</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;└─4803 nginx: worker process</span></div>
<div>&nbsp;</div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 15:00:31 localhost.localdomain systemd[1]: Starting nginx - high performance web server...</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 15:00:31 localhost.localdomain nginx[4798]: nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 15:00:31 localhost.localdomain nginx[4798]: nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 15:00:31 localhost.localdomain systemd[1]: Failed to read PID from file /usr/local/nginx/logs/nginx.pid: Invalid argument</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 15:00:31 localhost.localdomain systemd[1]: Started nginx - high performance web server.</span></div>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">设置nginx开机启动</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl enable nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">nginx安装完成，下面安装mysql</span></p>
<h4><strong><span style="font-family: Verdana; font-size: 10pt;">安装MySQL</span></strong></h4>
<p><span style="font-family: Verdana; font-size: 10pt;">安装依赖包</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost nginx-</span><span style="font-family: Verdana; font-size: 10pt;">1.12</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cd /software/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># yum -y install ncurses ncurses-devel bison cmake gcc gcc-c++</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">创建用户和组</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># groupadd mysql</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># useradd -s /sbin/nologin -g mysql mysql -M</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># id mysql</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">解压mysql源码包</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># </span><span style="font-family: Verdana; font-size: 10pt;">tar</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">zxvf</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">mysql-boost-5.7.18.tar.gz</span>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">进入解压后的目录，对mysql进行配置（5.5以上都是cmake）</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># cd mysql-5.7.18/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql-</span><span style="font-family: Verdana; font-size: 10pt;">5.7</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">18</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock -DSYSCONFDIR=/usr/local/mysql/etc -DSYSTEMD_PID_DIR=/usr/local/mysql -DDEFAULT_CHARSET=utf8 &nbsp;-DDEFAULT_COLLATION=utf8_general_ci -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DMYSQL_DATADIR=/usr/local/mysql/data -DWITH_BOOST=boost -DWITH_SYSTEMD=1</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">记住这个/usr/local/mysql/mysql.sock，php连接mysql会用到。</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">编译和安装</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql-</span><span style="font-family: Verdana; font-size: 10pt;">5.7</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">18</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># make &amp;&amp; make install</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">初始化数据库及启动</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql-</span><span style="font-family: Verdana; font-size: 10pt;">5.7</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">18</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># chown -R mysql.mysql /usr/local/mysql/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql-</span><span style="font-family: Verdana; font-size: 10pt;">5.7</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">18</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cd /usr/local/mysql/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">cat &gt;&gt; my.cnf &lt;&lt; EOF</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[client]</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">port = 3306</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">default-character-set=utf8</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">socket = /usr/</span><span style="font-family: Verdana; font-size: 10pt;">local</span><span style="font-family: Verdana; font-size: 10pt;">/mysql/mysql.sock</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">[mysql]</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">port = 3306</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">default-character-set=utf8</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">socket = /usr/</span><span style="font-family: Verdana; font-size: 10pt;">local</span><span style="font-family: Verdana; font-size: 10pt;">/mysql/mysql.sock</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">[mysqld]</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">user = mysql</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">basedir = /usr/</span><span style="font-family: Verdana; font-size: 10pt;">local</span><span style="font-family: Verdana; font-size: 10pt;">/mysql</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">datadir = /usr/</span><span style="font-family: Verdana; font-size: 10pt;">local</span><span style="font-family: Verdana; font-size: 10pt;">/mysql/data</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">port = 3306</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">default-character-set=utf8</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">pid-file = /usr/</span><span style="font-family: Verdana; font-size: 10pt;">local</span><span style="font-family: Verdana; font-size: 10pt;">/mysql/mysqld.pid</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">socket = /usr/</span><span style="font-family: Verdana; font-size: 10pt;">local</span><span style="font-family: Verdana; font-size: 10pt;">/mysql/mysql.sock</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">server-id = 1</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;"># Remove leading # to set options mainly useful for reporting servers.# The server defaults are faster for transactions and fast SELECTs.# Adjust sizes as needed, experiment to find the optimal values.# join_buffer_size = 128M# sort_buffer_size = 2M# read_rnd_buffer_size = 2M </span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">EOF</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># chown mysql.mysql my.cnf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># echo 'PATH=/usr/local/mysql/bin:/usr/local/mysql/lib:$PATH' &gt;&gt; /etc/profile</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># echo 'export PATH' &gt;&gt; /etc/profile</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># source /etc/profile</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># bin/mysqld --initialize-insecure --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># cp usr/lib/systemd/system/mysqld.service /usr/lib/systemd/system/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl daemon-reload </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl start mysqld</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost data]</span><span style="font-family: Verdana; font-size: 10pt;"># ps -ef|grep mysql</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">设置mysql开机启动</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl enable mysqld</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">查看Mysql启动状态</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl status mysqld</span></p>
<div><span style="font-size: 10pt; font-family: Verdana;">[root@localhost ~]# systemctl status mariadb</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">● mariadb.service - MariaDB database server</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp;Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp;Active: active (running) since Mon 2018-03-19 14:40:49 CST; 34min ago</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp;Main PID: 1585 (mysqld_safe)</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp;CGroup: /system.slice/mariadb.service</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;├─1585 /bin/sh /usr/bin/mysqld_safe --basedir=/usr</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;└─1748 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mariadb/mariadb.log...</span></div>
<div>&nbsp;</div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 14:40:47 localhost.localdomain systemd[1]: Starting MariaDB database server...</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 14:40:47 localhost.localdomain mariadb-prepare-db-dir[1554]: Database MariaDB is probably initialized in /var/lib/mysql already, nothing is done.</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 14:40:47 localhost.localdomain mysqld_safe[1585]: 180319 14:40:47 mysqld_safe Logging to '/var/log/mariadb/mariadb.log'.</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 14:40:47 localhost.localdomain mysqld_safe[1585]: 180319 14:40:47 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql</span></div>
<div><span style="font-size: 10pt; font-family: Verdana;">Mar 19 14:40:49 localhost.localdomain systemd[1]: Started MariaDB database server.</span></div>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">进入数据库，创建一个测试数据库以及授权远程用户可访问这个数据库</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># mysql</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Welcome to the MySQL monitor. &nbsp;Commands end </span><span style="font-family: Verdana; font-size: 10pt;">with</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;; </span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;\g.</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Your MySQL connection id </span><span style="font-family: Verdana; font-size: 10pt;">is</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">5</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Server version: </span><span style="font-family: Verdana; font-size: 10pt;">5.7.18</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;Source distribution</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Copyright (c) </span><span style="font-family: Verdana; font-size: 10pt;">2000</span><span style="font-family: Verdana; font-size: 10pt;">, </span><span style="font-family: Verdana; font-size: 10pt;">2017</span><span style="font-family: Verdana; font-size: 10pt;">, Oracle </span><span style="font-family: Verdana; font-size: 10pt;">and</span><span style="font-family: Verdana; font-size: 10pt;">/</span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;its affiliates. All rights reserved.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Oracle </span><span style="font-family: Verdana; font-size: 10pt;">is</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;a registered trademark of Oracle Corporation </span><span style="font-family: Verdana; font-size: 10pt;">and</span><span style="font-family: Verdana; font-size: 10pt;">/</span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;its</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">affiliates. Other names may be trademarks of their respective</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">owners.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Type </span><span style="font-family: Verdana; font-size: 10pt;">'help;'</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">or</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">'\h'</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">for</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;help. Type </span><span style="font-family: Verdana; font-size: 10pt;">'\c'</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;to clear the current input statement.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysql&gt; create database ceshi CHARACTER SET utf8 &nbsp;COLLATE utf8_general_ci;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Query OK, </span><span style="font-family: Verdana; font-size: 10pt;">1</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;row affected (</span><span style="font-family: Verdana; font-size: 10pt;">0.00</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;sec)</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysql&gt; grant all on ceshi.* to ceshi@</span><span style="font-family: Verdana; font-size: 10pt;">'%'</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;identified by </span><span style="font-family: Verdana; font-size: 10pt;">'ceshi2017'</span><span style="font-family: Verdana; font-size: 10pt;">;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Query OK, </span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;rows affected, </span><span style="font-family: Verdana; font-size: 10pt;">1</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;warning (</span><span style="font-family: Verdana; font-size: 10pt;">0.00</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;sec)</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysql&gt; flush privileges;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Query OK, </span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;rows affected (</span><span style="font-family: Verdana; font-size: 10pt;">0.01</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;sec)</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">查看授权的用户表</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># mysql</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Welcome to the MySQL monitor. &nbsp;Commands end with ; </span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;\g.</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Server version: </span><span style="font-family: Verdana; font-size: 10pt;">5.7.18</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;Source distribution</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Oracle is a registered trademark of Oracle Corporation </span><span style="font-family: Verdana; font-size: 10pt;">and</span><span style="font-family: Verdana; font-size: 10pt;">/</span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;its</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">affiliates. Other names may be trademarks of their respective</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">owners.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Type </span><span style="font-family: Verdana; font-size: 10pt;">'help;'</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">or</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">'\h'</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">for</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;help. Type </span><span style="font-family: Verdana; font-size: 10pt;">'\c'</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;to clear the current input statement.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysql&gt; SELECT DISTINCT CONCAT(</span><span style="font-family: Verdana; font-size: 10pt;">'User: '''</span><span style="font-family: Verdana; font-size: 10pt;">,user,</span><span style="font-family: Verdana; font-size: 10pt;">'''@'''</span><span style="font-family: Verdana; font-size: 10pt;">,host,</span><span style="font-family: Verdana; font-size: 10pt;">''';'</span><span style="font-family: Verdana; font-size: 10pt;">) </span><span style="font-family: Verdana; font-size: 10pt;">AS</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;query FROM mysql.user;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------------------+</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| query &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------------------+</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| User: </span><span style="font-family: Verdana; font-size: 10pt;">'ceshi'</span><span style="font-family: Verdana; font-size: 10pt;">@</span><span style="font-family: Verdana; font-size: 10pt;">'%'</span><span style="font-family: Verdana; font-size: 10pt;">; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| User: </span><span style="font-family: Verdana; font-size: 10pt;">'mysql.sys'</span><span style="font-family: Verdana; font-size: 10pt;">@</span><span style="font-family: Verdana; font-size: 10pt;">'localhost'</span><span style="font-family: Verdana; font-size: 10pt;">; |</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| User: </span><span style="font-family: Verdana; font-size: 10pt;">'root'</span><span style="font-family: Verdana; font-size: 10pt;">@</span><span style="font-family: Verdana; font-size: 10pt;">'localhost'</span><span style="font-family: Verdana; font-size: 10pt;">; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------------------+</span><span style="font-family: Verdana; font-size: 10pt;">3</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;rows in set (</span><span style="font-family: Verdana; font-size: 10pt;">0.00</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;sec)</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">在别的机器连接172.17.11.186的ceshi数据库</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost ~]</span><span style="font-family: Verdana; font-size: 10pt;"># mysql -h172.16.0.20 -uceshi -p'ceshi2017'mysql:</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;[Warning] Using a password on the command line interface can be insecure.</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Welcome to the MySQL monitor. &nbsp;Commands </span><span style="font-family: Verdana; font-size: 10pt;">end</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;with ; </span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;\g.</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Your MySQL connection id is </span><span style="font-family: Verdana; font-size: 10pt;">11</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Server </span><span style="font-family: Verdana; font-size: 10pt;">version:</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">5.7</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">18</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;Source distribution</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Copyright (c) </span><span style="font-family: Verdana; font-size: 10pt;">2000</span><span style="font-family: Verdana; font-size: 10pt;">, </span><span style="font-family: Verdana; font-size: 10pt;">2017</span><span style="font-family: Verdana; font-size: 10pt;">, Oracle </span><span style="font-family: Verdana; font-size: 10pt;">and</span><span style="font-family: Verdana; font-size: 10pt;">/</span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;its affiliates. All rights reserved.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Oracle is a registered trademark of Oracle Corporation </span><span style="font-family: Verdana; font-size: 10pt;">and</span><span style="font-family: Verdana; font-size: 10pt;">/</span><span style="font-family: Verdana; font-size: 10pt;">or</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;its</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">affiliates. Other names may be trademarks of their respective</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">owners.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Type </span><span style="font-family: Verdana; font-size: 10pt;">'help;'</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">or</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">'\h'</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">for</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;help. Type </span><span style="font-family: Verdana; font-size: 10pt;">'\c'</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;to clear the current input statement.</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysql&gt; show databases;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------+</span><span style="font-family: Verdana; font-size: 10pt;">| Database &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------+</span><span style="font-family: Verdana; font-size: 10pt;">| information_schema || ceshi &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------+</span><span style="font-family: Verdana; font-size: 10pt;">2</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;rows </span><span style="font-family: Verdana; font-size: 10pt;">in</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;set (</span><span style="font-family: Verdana; font-size: 10pt;">0</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">00</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;sec)</span></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h4><strong><span style="font-family: Verdana; font-size: 10pt;">PHP 7 安装</span></strong></h4>
<p><span style="font-family: Verdana; font-size: 10pt;">PHP 7 在15年年底推出，PHP官方说的比 PHP 5 快2倍。不过有个很值得注意的地方是，虽然 PHP 7 增加了不少新特性，但也很多地方是向后不兼容的，例如 mysql 扩展，在 PHP 7 中已经被删除。 现在最新版本是7.4.9。</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">进入software目录</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost mysql]</span><span style="font-family: Verdana; font-size: 10pt;"># cd /software/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">接着解压php源码包</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># </span><span style="font-family: Verdana; font-size: 10pt;">tar</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">zxvf</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">php-7.1.4.tar.gz</span>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">再进入解压后的文件夹</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost software]</span><span style="font-family: Verdana; font-size: 10pt;"># cd php-7.1.4/</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">这里将只安装一些常用的扩展，可以根据自己的实际需要进行增减，可以通过以下命令查看PHP安装是具体有有些扩展和选项：</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># ./configure --help</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">有接近300个选项。</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">安装之前要先安装那些准备装的扩展要用到的软件模块</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># yum -y install libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel curl curl-devel openssl openssl-devel</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">接下来 configure PHP 7</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># ./configure --prefix=/usr/local/php --enable-fpm --with-fpm-user=nginx --with-fpm-group=nginx --with-mysqli --with-zlib --with-curl --with-gd --with-jpeg-dir --with-png-dir --with-freetype-dir --with-openssl --enable-mbstring --enable-xml --enable-session --enable-ftp --enable-pdo -enable-tokenizer --enable-zip</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">上面已经提到，PHP 7 已经删除了 MySQL 扩展，所以 -with-mysql 不再是一个有效的选项。这里用 MySQLi 或 PDO 代替。</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">其中 --prefix 是安装目录，上面提到在同一个服务器安装多个 PHP 版本，这个 --prefix 设定是很有必要的。至于其他扩展大家按实际增减。</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">如果 configure 成功的话，将会看到以下类似字样：</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------------------------------------------------------+</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| License: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| This software is subject to the PHP License, available </span><span style="font-family: Verdana; font-size: 10pt;">in</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">this</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| distribution </span><span style="font-family: Verdana; font-size: 10pt;">in</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;the file LICENSE. &nbsp;By continuing </span><span style="font-family: Verdana; font-size: 10pt;">this</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;installation |</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| process, you are bound by the terms </span><span style="font-family: Verdana; font-size: 10pt;">of</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">this</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;license agreement. &nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| If you </span><span style="font-family: Verdana; font-size: 10pt;">do</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;not agree </span><span style="font-family: Verdana; font-size: 10pt;">with</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;the terms </span><span style="font-family: Verdana; font-size: 10pt;">of</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">this</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;license, you must abort |</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">| the installation process at </span><span style="font-family: Verdana; font-size: 10pt;">this</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;point. &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">+--------------------------------------------------------------------+</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">Thank you </span><span style="font-family: Verdana; font-size: 10pt;">for</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;using PHP.</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">编译和安装</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># make &amp;&amp; make install</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">好，PHP 7 已经安装完成，下面进行配置</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">先是 PHP 的配置文档</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cp php.ini-development /usr/local/php/lib/php.ini</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">php.ini 路径应该放在 PREFIX/lib 文件夹，除非在安装的时候通过这个选项修改</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">--with-config-file-path=PATH</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">如果安装 PHP 时没有指明 --prefix ，那么就 php.ini 路径就是 /usr/local/lib/php.ini 。刚才安装时有指明 --prefix ，所以是 /usr/local/php/lib/php.ini</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">然后根据实际自己需要修改 php.ini。</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">查找 mysqli.default_socket，修改成 mysqli.default_socket = /usr/local/mysql/mysql.sock：</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep mysqli.default_socket &nbsp;/usr/local/php/lib/php.ini</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysqli.default_socket = </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># sed -i 's#mysqli.default_socket =#mysqli.default_socket = /usr/local/mysql/mysql.sock#' &nbsp;/usr/local/php/lib/php.ini</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep mysqli.default_socket &nbsp;/usr/local/php/lib/php.ini</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysqli.default_socket = </span><span style="font-family: Verdana; font-size: 10pt;">/usr/local/mysql/mysql</span><span style="font-family: Verdana; font-size: 10pt;">.sock</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">其中 /usr/local/mysql/mysql.sock 就是上面安装 MySQL 时提到的。这个值必须填，否则会出现如下错误：</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">Warning: mysqli_connect(): (HY000/2002): No such file or directory</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">修改时区，查找 date.timezone，改成（主要将前面的 ; 去掉，这个是注释用的）：</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep date.timezone /usr/local/php/lib/php.ini </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">; </span><span style="font-family: Verdana; font-size: 10pt;">http:</span><span style="font-family: Verdana; font-size: 10pt;">/</span><span style="font-family: Verdana; font-size: 10pt;">/php.net/date</span><span style="font-family: Verdana; font-size: 10pt;">.timezone</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">;date.timezone =</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># sed -i 's#;date.timezone =#date.timezone = Asia/Shanghai#' /usr/local/php/lib/php.ini </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep date.timezone /usr/local/php/lib/php.ini </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">; </span><span style="font-family: Verdana; font-size: 10pt;">http:</span><span style="font-family: Verdana; font-size: 10pt;">/</span><span style="font-family: Verdana; font-size: 10pt;">/php.net/date</span><span style="font-family: Verdana; font-size: 10pt;">.timezone</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">date.timezone = Asia/Shanghai</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">好了，PHP 7 已经安装好，下面验证一下</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># /usr/local/php/bin/php -v</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">PHP </span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;(cli) (</span><span style="font-family: Verdana; font-size: 10pt;">built:</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;Apr </span><span style="font-family: Verdana; font-size: 10pt;">17</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">2017</span>&nbsp;<span style="font-family: Verdana; font-size: 10pt;">14:58:11</span><span style="font-family: Verdana; font-size: 10pt;">) ( NTS )</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Copyright (c) </span><span style="font-family: Verdana; font-size: 10pt;">1997</span><span style="font-family: Verdana; font-size: 10pt;">-</span><span style="font-family: Verdana; font-size: 10pt;">2017</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;The PHP Group</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Zend Engine v3.</span><span style="font-family: Verdana; font-size: 10pt;">1.0</span><span style="font-family: Verdana; font-size: 10pt;">, Copyright (c) </span><span style="font-family: Verdana; font-size: 10pt;">1998</span><span style="font-family: Verdana; font-size: 10pt;">-</span><span style="font-family: Verdana; font-size: 10pt;">2017</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;Zend Technologies</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">再查看下已经安装的模块</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-7.1.4]</span><span style="font-family: Verdana; font-size: 10pt;"># /usr/local/php/bin/php -m</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[PHP Modules]</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Core</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ctype</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">curl</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">date</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">dom</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">fileinfo</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">filter</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ftp</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">gd</span><span style="font-family: Verdana; font-size: 10pt;">hash</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">iconv</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">json</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">libxml</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">mbstring</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysqli</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">mysqlnd</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">openssl</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">pcre</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">PDO</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">pdo_sqlite</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Phar</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">posix</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Reflection</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">session</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">SimpleXML</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">SPL</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">sqlite3</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">standard</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">tokenizer</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">xml</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">xmlreader</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">xmlwriter</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">zip</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">zlib</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[Zend Modules]</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">接下来配置 php-fpm，复制 php-fpm 的配置文档</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">修改 /usr/local/php/etc/php-fpm.d/www.conf</span><span style="font-family: Verdana; font-size: 10pt;">，把启动用户改为和nginx服务同一个启动用户（前面Nginx使用的是www账户，这里改成和Nginx使用一样的账户；一般都是使用www用户）</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep -E 'user =|group =' /usr/local/php/etc/php-fpm.d/www.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">user = nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">group = nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># sed -i 's#user = nginx#user = www#' /usr/local/php/etc/php-fpm.d/www.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># sed -i 's#group = nginx#group = www#' /usr/local/php/etc/php-fpm.d/www.conf </span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep -E 'user =|group =' /usr/local/php/etc/php-fpm.d/www.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">user = www</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">group = www</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">;listen.group = www</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">其中www.conf要留意这个值 listen = 127.0.0.1:9000</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep 'listen = 127.0.0.1' /usr/local/php/etc/php-fpm.d/www.conf</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">这里使用 9000 端口，这个选项在配置 Nginx 网站时要用到的。</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">配置 php-fpm 启动服务脚本</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cp sapi/fpm/php-fpm.service /usr/lib/systemd/system/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">查看启动脚本中指定的程序目录和pid文件（默认已经修改过了，如果没有修改过执行下面操作）</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># grep -E 'PIDFile|ExecStart' /usr/lib/systemd/system/php-fpm.service</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">PIDFile=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/local/php/var/run/php</span><span style="font-family: Verdana; font-size: 10pt;">-fpm.pid</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ExecStart=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/local/php/sbin/php-fpm --nodaemonize --fpm-config /usr/local/php/etc/php</span><span style="font-family: Verdana; font-size: 10pt;">-fpm.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">修改启动脚本，把里边 prefix 相关的内容用实际路径代替</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># vim /usr/lib/systemd/system/php-fpm.service</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">将</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">PIDFile=${prefix}/var/run/php-fpm.pid</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ExecStart=${exec_prefix}/sbin/php-fpm --nodaemonize --fpm-config ${prefix}/etc/php-fpm.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">修改成</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">PIDFile=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/local/php/var/run/php</span><span style="font-family: Verdana; font-size: 10pt;">-fpm.pid</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">ExecStart=</span><span style="font-family: Verdana; font-size: 10pt;">/usr/local/php/sbin/php-fpm --nodaemonize --fpm-config /usr/local/php/etc/php</span><span style="font-family: Verdana; font-size: 10pt;">-fpm.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">重新载入 systemd</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl daemon-reload</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">让 php-fpm 随机启动</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php</span><span style="font-family: Verdana; font-size: 10pt;">-7.1.4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl enable php-fpm</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">Created symlink </span><span style="font-family: Verdana; font-size: 10pt;">from</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;/etc/systemd/system/multi-user.target.wants/php-fpm.service to /usr/lib/systemd/system/php-fpm.service.</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">立即启动 php-fpm</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl start php-fpm</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">查看状态</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-7.1.4]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl status php-fpm</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">● php-fpm.service - The PHP FastCGI Process Manager</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;Loaded: loaded (/usr/lib/systemd/system/php-fpm.service; enabled; vendor preset: disabled)</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;Active: active (running) since 一 2017-04-17 15:37:06 CST; 1min 9s ago</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;Main PID: 55770 (php-fpm)</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;CGroup: /system.slice/php-fpm.service</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;├─55770 php-fpm: master process (/usr/</span><span style="font-family: Verdana; font-size: 10pt;">local</span><span style="font-family: Verdana; font-size: 10pt;">/php/etc/php-fpm.conf)</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;├─55771 php-fpm: pool www</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└─55772 php-fpm: pool www</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">4月 17 15:37:06 localhost.localdomain systemd[1]: Started The PHP FastCGI Process Manager.</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">4月 17 15:37:06 localhost.localdomain systemd[1]: Starting The PHP FastCGI Process Manager...</span></p>
<p>&nbsp;</p>
<p><span style="font-family: Verdana; font-size: 10pt;">好，php-fpm 已经成功启动，那就立即建个网站看看</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">配置 Nginx 站点</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">先建立一个 lnmp 站点，路径是 /var/www/html</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># mkdir -p /var/www/html</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># chown -R www.www /var/www</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">并准备好 phpinfo 测试文件</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">cat &gt;&gt; /</span><span style="font-family: Verdana; font-size: 10pt;">var</span><span style="font-family: Verdana; font-size: 10pt;">/www/html/test.php &lt;&lt; EOF</span><span style="font-family: Verdana; font-size: 10pt;">&lt;?php</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">phpinfo();</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">EOF</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">创建一个 Nginx 配置文件放到 /usr/local/nginx/conf/conf.d 中</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost php-</span><span style="font-family: Verdana; font-size: 10pt;">7.1</span><span style="font-family: Verdana; font-size: 10pt;">.</span><span style="font-family: Verdana; font-size: 10pt;">4</span><span style="font-family: Verdana; font-size: 10pt;">]</span><span style="font-family: Verdana; font-size: 10pt;"># cd /usr/local/nginx/conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost conf]</span><span style="font-family: Verdana; font-size: 10pt;"># sed -i '$i\include /usr/local/nginx/conf/conf.d/*;' nginx.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost conf]</span><span style="font-family: Verdana; font-size: 10pt;"># cat nginx.conf</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost conf]</span><span style="font-family: Verdana; font-size: 10pt;"># mkdir conf.d</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost conf]</span><span style="font-family: Verdana; font-size: 10pt;"># cd conf.d/</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">创建test.com.conf文件并写入以下内容</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">cat &gt;&gt; test.com.conf &lt;&lt;EOF</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">server {</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;listen &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="font-family: Verdana; font-size: 10pt;">81</span><span style="font-family: Verdana; font-size: 10pt;">;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;server_name &nbsp;localhost;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;root &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/</span><span style="font-family: Verdana; font-size: 10pt;">var</span><span style="font-family: Verdana; font-size: 10pt;">/www/html;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;location / {</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;index &nbsp;index.php index.html index.htm;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;}</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;location ~ \.php$ {</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fastcgi_pass &nbsp;&nbsp;</span><span style="font-family: Verdana; font-size: 10pt;">127.0.0.1</span><span style="font-family: Verdana; font-size: 10pt;">:</span><span style="font-family: Verdana; font-size: 10pt;">9000</span><span style="font-family: Verdana; font-size: 10pt;">;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fastcgi_index &nbsp;index.php;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fastcgi_param &nbsp;SCRIPT_FILENAME &nbsp;&nbsp;&nbsp;\$document_root\$fastcgi_script_name;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fastcgi_param &nbsp;PHP_VALUE &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;open_basedir=\$document_root:/tmp/:/proc/;</span></p>
<p><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="font-family: Verdana; font-size: 10pt;">include</span><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fastcgi_params;</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">&nbsp;&nbsp;&nbsp;&nbsp;}</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">}</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">EOF</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">其中 server_name localhost; 中的 localhost 改成你自己的域名（例如：</span><a href="https://link.jianshu.com/?t=http://www.baidu.com"><span style="text-decoration: underline;"><span style="font-family: Verdana; color: #0000ff; font-size: 10pt;">www.baidu.com</span></span></a><span style="font-family: Verdana; font-size: 10pt;">，这里我直接使用localhost和81端口来测试。网站域名解析默认都是使用80端口的）</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">其中 root /var/www/html; 就是刚才创建的站点目录</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">其中 fastcgi_pass 127.0.0.1:9000; 就是上面配置 php-fpm 提到要留意的值</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">修改配置后一定要记得 reload nginx 才能生效</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost conf.d]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl reload nginx</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">[root@localhost conf.d]</span><span style="font-family: Verdana; font-size: 10pt;"># systemctl reload php-fpm</span></p>
<p><span style="font-family: Verdana; font-size: 10pt;">最后的配置（nginx服务器的IP必须和域名做解析，才可以使用域名访问服务，域名购买一般在阿里云上购买）</span><span style="mso-spacerun: 'yes'; font-family: 新宋体; font-size: 9.0000pt;"><br /></span><span style="font-family: Verdana; font-size: 10pt;">这里我们直接使用IP:81访问（因为我们使用的端口是80；域名是localhost，也就是nginx所在主机IP的意思）</span></p>
<p>&nbsp;</p>
