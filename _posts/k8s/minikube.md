# Setup

## 安装 docker

首先我们需要安装 `docker` 来打包镜像，如果你本地已经安装了 `docker`，那么你可以选择跳过这一小节。

推荐使用 Docker Desktop来安装 docker 。打开官网下载对应你电脑操作系统的包即可 (https://www.docker.com/products/docker-desktop/)，

当安装完成后，可以通过 `docker run hello-world` 来快速校验是否安装成功！

## docker中启动k8s

安装docker desktop 后，可以很方便的启动一个单节点k8s，参考链接：docker 安装k8s 教程：https://docs.docker.com/desktop/kubernetes/

1. 打开docker，选择 右上角的设置图标.
2. 选择 **Kubernetes** 
3. 点击 **Enable Kubernetes**,
4. 选择  **Apply & Restart**

安装完成后，测试kubectl命令

```
kubectl version
```

## 代码启动

首先下载好代码，然后按照下面命令一步步执行。

```shell
# 构建docker镜像 
cd <项目根目录>
#下载doc，用以构建doc镜像
git submodule update --init --recursive 

docker build -f tcs-config/doc/Dockerfile docs/ -t open-platform-doc:dev

docker build -f tcs-config/krakend/Dockerfile krakend/ -t krakend:dev 

docker build -f tcs-config/fake_api/Dockerfile data/ -t fake_api:dev

docker build -f tcs-config/keycloak/Dockerfile keycloak/ -t keycloak:dev

docker build -f grafana/Dockerfile grafana/ -t grafana:dev

docker pull postgres:14.2

docker pull influxdb:1.8

docker pull registry.k8s.io/ingress-nginx/controller:v1.5.1@sha256:4ba73c697770664c1e00e9f968de14e08f606ff961c76e5d7033a4a9c593c629

docker pull registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20220916-gd32f8c343@sha256:39c5b2e3310dc4264d638ad28d9d1d96c4cbb2b2dcfb52368fe4e3c63f61e10f

# 部署服务，成功后可以使用ip:nodeport的形式访问服务
cd tcs-config/dev

kubectl apply -f doc-deployment.yaml 

kubectl apply -f krakend-deployment.yaml

kubectl apply -f keycloak-deployment.yaml

kubectl apply -f grafana-deployment.yaml

# kubectl apply -f fake_api-deployment.yaml fake_api暂时不可用

# 启动ingress-nginx服务，成功后可以使用ip/path的形式访问服务

kubectl apply -f ingress-nginx.yaml

kubectl apply -f router.yaml

# 启动的所有服务如下
# doc http://127.0.0.1:30000/ 或者 http://127.0.0.1/docs
# krakend http://127.0.0.1:30001/
# influxdb http://127.0.0.1:30002/
# keycloak http://127.0.0.1:30003/
# keycloak-db http://127.0.0.1:30004/
# grafana http://127.0.0.1:30005/
```

脚本执行成功后，所有启动的服务如下，可以访问doc服务查看是否部署成功。

- doc http://127.0.0.1:30000/ 或者 http://127.0.0.1/docs
- krakend http://127.0.0.1:30001/
- influxdb http://127.0.0.1:30002/
- keycloak http://127.0.0.1:30003/
- keycloak-db http://127.0.0.1:30004/
- grafana http://127.0.0.1:30005/

## 常用k8s命令

一些常用的k8s命令，部署，调试过程中可能有用

查看pods，service，pv，pvc

```
kubectl get pods 
kubectl get service
kubectl get pv
kubectl get pvc
```

查看pods 日志

```
kubectl logs <pod_name> 
```

修改deployment文件后，重新部署

```
kubectl apply -f <deployment.yaml>
```

强制重新部署（等价于先删除再部署，常用于镜像改变了，但deployment文件未变）

```
kubectl replace --force -f <deployment.yaml>
```

清空所有服务

```
kubectl delete deployment,service,pv,pvc --all
```

参考文档：

- k8s教程：https://github.com/guangzhengli/k8s-tutorials