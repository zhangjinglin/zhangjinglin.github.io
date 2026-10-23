---
title: "Ubuntu PHP 开发环境配置"
date: 2016-03-08T05:38:26+00:00
url: "/2016/03/ubuntu-php/"
draft: false
---

如何设置Ubuntu Server和PHP开发环境的配置

## 下载并安装 [VirtualBox](https://www.virtualbox.org)

## 下载 ubuntu-14.10-server-amd64.iso

## 建立虚拟机并安装 Ubuntu Server

1. 设定网络模式为 Networl Brige，网桥模式
2. 安装，服务都不选，我们自行安装，不选择自动更新

## 第一次启动虚拟机

1 建立虚拟机快照

2 检查网络是否配置成功

```bash
ip add
```





3 如果没有DHCP，请设定静态地址

```bash
sudo vi  /etc/network/interfaces

#静态地址设置方法
auto eth0
iface eth0 inet static
address x.x.x.x
netmask x.x.x.x
gateway x.x.x.x

#设定虚拟ip
auto eth0:1
iface eth0 inet static
address x.x.x.x
netmask x.x.x.x

up route add -net x.x.x.x/x gw x.x.x.x
```



4 重启网络，检查是否成功

```bash
# problem
# sudo /etc/init.d/networking restart
sudo ifdown eth0
sudo ifup eth0
sudo ifdown eth0:1
sudo ifup eth0:1
ping x.x.x.x
```



5 设定主机名称

```bash
sudo vi /etc/hostname
```



6 设定dns

```bash
sudo vi /etc/resolv.conf
sudo vi /etc/resolvconf/resolv.conf.d/base
  nameserver x.x.x.x
sudo resolvconf -u
```





## 更新系统

1. 修改软件源
```bash
#备份原始配置文件
sudo cp /etc/apt/sources.list /etc/apt/sources.list.ori
sudo vi /etc/apt/sources.list
#将us修改为cn
```

2. 更新系统
```bash
sudo apt-get update
sudo apt-get dist-upgrade
```




## SSHD 服务

1. 安装openssh-server
```bash
sudo apt-get install openssh-server
#确保显示
ps -e | grep sshd
```

2. ssh到虚拟机
```bash
ssh xxx.xxx.xxx.xxx
```




## 安装 MySQL

1. 安装 MySQL
```plain
sudo apt-get install mysql-server
```

2. 检查是否启动
```plain
ps -e | grep mysql
#确保出现 mysqld
```

3. 在生产环境中，设置数据库安全，本地使用可略过
```plain
sudo mysql_secure_installation
```

4. 测试数据库
```plain
mysql -u root -p
```




## 安装 Apache

1. 安装 apache
```bash
sudo apt-get install apache2
#检查Apache服务是否启动
ps -e | grep apa
```

2. 主机浏览器虚拟机查看测试页面

## 安装 PHP

1. 安装 PHP
```bash
sudo apt-get install php5
```

2. 测试 PHP
```bash
php --version
```

3. 重新启动Apache
```plain
sudo /etc/init.d/apache2 restart
```

4. 测试PHP与Apache
```bash
#新建测试文件
sudo vi /var/www/html/info.php
```

5. info.php
```php
<?php
  phpinfo();
?>
```

6. 增加PHP-MYSQL支持
```plain
sudo apt-get install php5-mysql
sudo /etc/init.d/apache2 restart
```




## 安装 Composer

1. 安装
```bash
cd /tmp/
curl http://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
#测试
composer --version
```

2. 修改composer为中国镜像
```bash
#找到composer的配置文件
composer config -l -g
```

3. 修改配置文件为：
````
  {
    "config": {

    },
    "repositories": [
        {"type": "composer", "url": "http://pkg.phpcomposer.com/repo/packagist/"},
        {"packagist": false}
    ]
  }

## 安装 PHP Mcrypt 模块
1. 安装 php5-mctypt
  ```bash
  sudo apt-get install php5-mcrypt
````

4. 开启模块
```plain
sudo php5enmod mcrypt
```

5. 重启apache

## 设置共享文件夹

1. 在虚拟机设置中增加共享文件夹
2. 正常启动虚拟机
3. 选择device->Insert Guest Addtions CD Images
4. 挂载光盘
```bash
sudo mount /dev/cdrom /media/cdrom/
```

5. 安装必要软件
```bash
sudo apt-get install gcc make
```

6. 安装增强工具
```bash
sudo /media/cdrom/VBoxLinuxAdditions.run
```

7. 挂载共享文件夹
```bash
cd ~
mkdir host
sudo mount -t vboxsf -o uid=$UID,gid=$(id -g) xxxxx(共享名称) host
```




## (可选）设置PHPStorm/WebStorm开发

1. 修改权限
```bash
sudo chown 用户名:用户组 /var/www/html
sudo chown 用户名:用户组 /var/www/html/*
```

2. 启动PHPStorm/， 选择 Create New Project form Existing Files，然后选择 Web server is on remote host, files are accessible via FTP/SFTP/FTPS，下一步后，设置项目名称和本地存储位置，在Specify parameters for a new server 中选择sftp，并设置参数，主机、用户名、口令，然后 Test SFTP connection，测试成功后选择root path，这里是/var/www/html。
3. 设定完成后，在phpstorm/webstorm 中设置：Upload changed files automatically to the default server: On explicit save action(command+s)，你可以自行选择。
4. 测试吧。
