# Lab08 实验前准备：创建一个 Azure Container Registry

## 1. 登录到 Azure

## 2. 创建 ACR 资源组
```console
$ az group create --name MWIT-ACR-RG --location koreacentral
```
>注意：资源组名称在 Azure 全球必须唯一，不能重名。

## 3. 创建 ACR
```console
$ az acr create --resource-group MWIT-ACR-RG --name mwitacrkoreacentral --sku Basic --admin-enabled true
```
>注意: acr name 在 Azure 全球必须唯一，不能重名。并且由于 acr name 大小写敏感，建议一律使用小写，防止出现意外情况。

## 4. 登录 ACR
```console
$ az acr login --name mwitacrkoreacentral
Login Succeeded
```
>重要: 如果用 az acr login 有问题，可以用 docker login 登录 ACR。
```console
$ docker login <Your-ACR-Login-Server> -u <Your-ACR-Username> -p <Your-ACR-Password>
$ docker login mwitacrkoreacentral.azurecr.io -u mwitacrkoreacentral -p XXXXXXXXXXX
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

## 5. 推送 Docker 镜像到 ACR
列出 ACR Login Server 全名
```console
$ az acr list --resource-group  MWIT-ACR-RG --query "[].{acrLoginServer:loginServer}" --output table
AcrLoginServer
------------------------------
mwitacrkoreacentral.azurecr.io
```
为 Docker 镜像打 Tag
```console
$ docker tag azure-vote-front mwitacrkoreacentral.azurecr.io/azure-vote-front:v1.0
```
推送 Docker 镜像到 ACR
```console
$ docker push mwitacrkoreacentral.azurecr.io/azure-vote-front:v1.0
```
>重要: 推送 Docker 镜像到 ACR，在中国大陆网络环境下比较慢，建议配置 Docker 镜像加速器。

列出 ACR 中的 Docker 镜像
```console
$ az acr repository list --name mwitacrkoreacentral --output table
Result
--------------------
azure-vote-front
```
```console
$ az acr repository show-tags --name mwitacrkoreacentral --repository azure-vote-front --output table
Result
--------
v1.0
```
在 Azure Portal 中查看 Docker 镜像，点击 Container registries > mwitacrkoreacentral - Repositories
