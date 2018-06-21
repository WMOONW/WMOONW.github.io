---
layout: post
title: Ubuntu14搭建OpenVpn
date: 2018-06-21
categories: blog
tags: [Ubuntu,openVpn]
description: 在Ubuntu上搭建openVpn，实现内网穿透。

---


# OpenVpn搭建

> vps环境：ubuntu14.0

## Vps端安装

### 1. 安装openVpn服务

输入安装命令：
```shell
sudo apt-get –y install openvpn libssl-dev openssl
```

安装完成后查看版本：
```shell
openvpn –version
```
	 
记录下版本号。
	
###	2. 安装easy-rsa，用来制作证书
	
输入安装命令：
``` shell
sudo apt-get –y install easy-rsa
```
	
###	3. 生成证书

* 1. 生成CA证书
	
    在`/etc/openvpn/`目录下创建`easy-rsa`文件夹：
    ```shell
    sudo mkdir /etc/openvpn/easy-rsa/
    ```

	将`/usr/share/easy-rsa`目录下所有文件复制到`/etc/openvpn/easy-rsa/`下：
    ```
	sudo cp -r /usr/share/easy-rsa/* /etc/openvpn/easy-rsa/
    ```

	切换到`root`目录下： 
    ```shell
    sudo su
    ```

	进入`easy-rsa`文件夹：
    ```shell
    cd /etc/openvpn/easy-rsa/
    ```

	编辑`vars`文件：
    ```shell
    sudo vi /etc/openvpn/easy-rsa/vars
    ```

	根据实际情况更改文件中如下配置项：
    ```shell
	export KEY_COUNTRY=”CN”
    export KEY_PROVINCE=”TJ”
    export KEY_CITY=”TianJin”
    export KEY_ORG=”qszy”
    export KEY_EMAIL=”公司或个人的邮箱地址”
    export KEY_OU=”qszy”
    export KEY_NAME=”qszyvpn”
    ```

	使`vars`文件生效：
    ```
    source vars
    ./clean-all
    ```
 
	制作CA证书：
	```shell
    ./build-ca
    ```

	一路回车，完成后查看`keys`目录，应有`ca.crt`和`ca.key`两个文件：
	```shell
    ll keys/
    ```
			 
	把生成的`ca.crt`文件复制到`openvpn`启动目录下：
	```shell
    cp kyes/ca.crt /etc/openvpn
    ```

* 2. 生成server端证书

	输入命令：
    ```shell
    ./build-keys-server qszyvpn
    ```

	> 此处名称须与生成CA证书时，`vars`文件配置中的`export KEY_NAME`配置项名称一致）

	一路回车，需要输入`y/n`时，一律输入`y`。

    完成后，查看`keys`文件夹，应有`qszyvpn.crt`、`qszyvpn.csr`、`qszyvpn.key`三个文件。

    为服务器生成加密交换时的秘钥协议文件：
	```shell
    ./build-dh
    ```
		 
	此过程需要时间较长，耐心等待。

	完成后查看`keys`目录，应有`dh2018.pem`文件：
		 
	然后将`vpnilanni.crt`、`vpnilanni.key`、`dh2048.pem`都复制到`/etc/openvpn/`目录下：
	```shell
    cp keys/qszyvpn.crt keys/qszyvpn.key keys/dh2048.pem /etc/openvpn/
    ```

* 3. 生成Client端证书

	输入命令：
    ```shell
    ./build-key qszy
    ```

	一路回车，需要输入`y/n`时，一律输入`y`。

	查看`keys`文件夹，应有`qszy.crt`、`qszy.csr`、`qszy.key`三个文件。
	> 此处名称`qszy`可进行自定义设置，须为每台要与vps建立vpn连接的客户端生成唯一名称的证书。

### 4. server端配置

复制openvpn自带的模板配置文件到启动目录：
```shell
cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz /etc/openvpn/
```

进入`openvpn`目录解压文件：
```shell
cd /etc/openvpn/
gzip -d server.conf.gz
```

修改`server.conf`文件：
```shell
sudo vi /etc/openvpn/server.conf
```

找到并修改如下配置项：
```shell
proto tcp
cert qszyvpn.crt
key qszyvpn.key
dh dh2048.pem
```

修改完成后查看配置结果：
```shell
grep -vE “^#|^;|^$” server.conf
```
	 

### 5.启动

以上工作结束后，即可启动openvpn服务：
```shell
/etc/init.d/openvpn start
```
 
查看其端口占用：
```shell
netstat -tunlp |grep 1194
```
 

## 客户端安装(win10)

1. 下载所需证书。将`/etc/openvpn/easy-rsa/keys/`目录下的`ca.crt`、`qszy.crt`、`qszy.key`三个文件以及`/usr/share/doc/openvpn/examples/sample-config-files/`目录下的`client.conf`模板配置文件下载到本地。先将四个文件复制到用户目录下，并赋予`qszy.key`文件足够权限(777)，再进行下载。

2. 将下载下来的`client.conf`文件重命名为`client.ovpn`，然后进行编辑，找到并修改如下配置：
    ```shell
	proto tcp
	remote vps公网ip地址 1194
	ca ca.crt
	cert qszy.crt
	key qszy.key
    ```

3. 下载win客户端。进入`http://build.openvpn.net/downloads/releases/`镜像，找到与`openvpn`服务版本一致的exe安装文件下载，进行安装。

4.  安装完成后，将下载的4个文件放入同一个文件夹中，并将文件夹复制到`openvpn`安装目录的`config`文件夹下，文件夹名称可随意设置，只要保证其在`config`目录下名称唯一即可。

5. 以管理员打开`openvpnGUI`客户端，会在系统托盘生成一个图标，右键单击图标，点击`connect`，即可连接，连接成功后，会提示已为本机生成一个IP。
 


