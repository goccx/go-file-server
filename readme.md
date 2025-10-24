# go-file-server

基于 Gin + Vue + Element UI 的前后端分离文件管理系统

## ✨ 特性

- Casbin 的 RBAC 访问控制模型
- JWT 认证
- dex ldap 鉴权
- GORM 的数据库存储
- time/rate 令牌桶限速
- bleve 文件索引
- ftpserverlib ftp 服务端库

## 🎁 内置

1. 文件管理：文件的增删改查
1. 用户管理：用户是系统操作者，该功能主要完成系统用户配置。
1. 部门管理：配置系统组织机构（公司、部门、小组），树结构展现支持数据权限。
1. 角色管理：角色菜单权限分配、设置角色按机构进行数据范围权限划分。
1. 操作日志：系统正常操作日志记录和查询；系统异常信息日志记录和查询。
1. 登录日志：系统登录日志记录查询包含登录异常。
1. 服务监控：查看一些服务器的基本信息。
1. ftp 服务：兼容 ftp 协议，支持从 lftp 等客户端工具进行文件的增删改查

## 📦 本地开发

### 环境要求

go 1.21

mysql 8.2

redis 5.6(可选，默认使用内存)

### 获取代码

```bash
git clone https://github.com/goccx/go-file-server.git
```

### 启动说明

```bash
# 进入项目目录
cd go-file-server

# 修改配置
vi ./config/config.yml
# 更新整理依赖
go mod tidy

# 启动服务
go run main.go server -c ./config/config.yml
```

#### 构建 docker 镜像

```shell
# 编译镜像
docker build -t go-file-server .
```

### 前端项目

> 🎨 **前端项目地址**: [go-file-server-ui](https://github.com/goccx/go-file-server-ui)
>
> 前端使用 Vue + Element UI 构建，详细的开发说明请查看前端仓库。

<br>

## 初始用户

> 用户 admin

> 密码 123456

<br>

## 部署

### docker 部署

> ⚠️ 注意修改 config.yaml，密码部分都是弱密码

> ⚠️ 如果使用 Nginx 代理，请设置 `proxy_buffering off;`，以解决图表显示异常的问题

```shell
cd deploy/docker
# 启动
docker-compose up -d
```

### helm 部署

> 注意：修改 config.yaml，密码部分都是弱密码

> 前提要求

- Helm 3

- Kubernetes 1.20+

#### 部署中间件

```shell
cd deploy/helm

# 添加中间件helm仓库
helm repo add bitnami https://charts.bitnami.com/bitnami

# 安装mysql
helm install mysql bitnami/mysql --version 10.3.0 --values mysql-10.3.0-values.yaml

#安装redis(可选)
helm install redis bitnami/redis --version 20.1.4 --values redis-20.1.4-values.yaml
```

#### 部署 dex

> 如果启用 ldap 认证，需要部署该组件，否则请跳过此步骤

```shell
# 添加 dex helm 仓库
helm repo add dex https://charts.dexidp.io

# 安装dex(注意修改dex-0.19.1-values.yaml，按需配置)
helm install dex dex/dex --version 0.19.1 --values dex-0.19.1-values.yaml

```

#### 部署 app

> [点击前往 app helm 仓库](https://github.com/goccx/helm-charts/tree/master/go-file-server)

```shell
# 添加app helm仓库
helm repo add go-file-server https://goccx.github.io/helm-charts/

# 如果修改了上述中间values配置, config.yaml也需要对应修改
# 创建configMap
kubectl create configmap go-file-server --from-file=config.yaml=config.yaml


#app vuales配置中支持ingress和ingressRoute,需要创建对对应额度 tls secrets
kubectl create secret tls example.com --key example.com.key --cert example.com.crt

#安装前后端app
#安装之前请修改app-1.0.0-values.yaml，如existingConfigMap、persistence、ingress部分
helm install go-file-server go-file-server/go-file-server --version 1.0.0 --values app-1.0.0-values.yaml
```

## 预览

<img width="1440" height="812" alt="ui" src="https://github.com/user-attachments/assets/aea0abc2-0bdd-44bd-adaf-66887b06a4af" />

<img width="1440" height="900" alt="go-file-server" src="https://github.com/user-attachments/assets/775c4010-5425-4d16-a0b0-e1e544cc0734" />


<br>

## 🤝 特别感谢

1. [go-admin](https://github.com/go-admin-team/go-admin)
2. [ftpserverlib](https://github.com/fclairamb/ftpserverlib)
3. [dex](https://github.com/dexidp/dex)
