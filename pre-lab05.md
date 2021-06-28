# Lab05 创建 Ubuntu VM：JDK 11 + Tomcat 9

## 1. 登录到 Azure

## 2. 创建资源组
```console
$ az group create --name MWIT-Tomcat-RG --location japaneast
```

## 3. 创建 VM
```console
$ az vm create -g MWIT-Tomcat-RG --name Tomcat-VM --image UbuntuLTS --size Standard_D1 --admin-username azureuser --generate-ssh-keys --public-ip-address Tomcat-PIP --public-ip-address-allocation static
```

## 4. 打开端口 8080
```console
$ az vm open-port --port 8080 -g MWIT-Tomcat-RG --name Tomcat-VM
```

## 5. 安装 JDK 11
查看 VM 的私有 IP 地址和公共 IP 地址
```console
$ az vm list-ip-addresses -g MWIT-Tomcat-RG --name Tomcat-VM --output table
VirtualMachine    PublicIPAddresses    PrivateIPAddresses
----------------  -------------------  --------------------
Tomcat-VM     42.159.90.60         10.0.0.4
```

SSH 登录到 VM，安装 Open JDK 11
```console
$ ssh azureuser@42.159.90.60
azureuser@TomcatVM:~$ sudo apt-get update
azureuser@TomcatVM:~$ sudo apt install -y openjdk-11-jdk
azureuser@TomcatVM:~$ java -version
openjdk version "11.0.9.1" 2020-11-04
OpenJDK Runtime Environment (build 11.0.9.1+1-Ubuntu-0ubuntu1.18.04)
OpenJDK 64-Bit Server VM (build 11.0.9.1+1-Ubuntu-0ubuntu1.18.04, mixed mode, sharing)
```
> 使用 sudo apt install default-jdk 可以安装默认最新的 Open JDK

## 6. 安装并配置 Tomcat 9

SSH 登录到 VM，安装 Tomcat 9
```console
$ ssh azureuser@42.159.90.60
azureuser@TomcatVM:~$ sudo apt-get install -y tomcat9 tomcat9-docs tomcat9-examples tomcat9-admin
```
配置管理员权限
```console
azureuser@TomcatVM:~$ sudo vim /var/lib/tomcat9/conf/tomcat-users.xml
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<role rolename="manager-status"/>
<role rolename="manager-script"/>
<user username="tomcat" password="tomcat" roles="admin-gui, manager-gui, manager-jmx, mananger-status, manager-script"/>
```

开机自启动 Tomcat 9
```console
azureuser@TomcatVM:~$ sudo systemctl enable tomcat9
```

启动/停止/重启 Tomcat 9
```console
azureuser@TomcatVM:~$ sudo systemctl start tomcat9 
azureuser@TomcatVM:~$ sudo systemctl stop tomcat9
azureuser@TomcatVM:~$ sudo systemctl restart tomcat9
```

访问 http://42.159.90.60:8080 tomcat/tomcat

## 7. 实验结束，清理资源
卸载 Tomcat 9
```console
$ ssh azureuser@42.159.90.60
azureuser@TomcatVM:~$ sudo apt-get autoremove tomcat9
```

删除资源组
```console
$ az group delete -y --name MWIT-Tomcat-RG 
```
