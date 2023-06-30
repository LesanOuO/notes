---
title: "学习笔记Docker与Kubernetes"
date: 2022-03-27T10:26:15+08:00
draft: false
tags: ['Docker', 'K8S', '.NET']
categories: ['学习笔记']
---

本笔记为Docker与Kubernetes的实践笔记

## 将`.NET Core`的项目发布为Docker镜像

### 通过VS支持创建Docker镜像

第一步，创建一个空的`.NET Core`项目，用来测试

第二步，为项目添加Docker支持

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker1.png)

添加成功后会在项目根目录创建Dockerfile，以及在Docker Desktop中创建对应的image及container，如下图所示

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker4.png)

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker2.png)

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker3.png)



### 通过已发布的文件包创建Docker镜像

第一步，将项目发布到文件夹

第二步，去到发布的文件夹下，添加Dockerfile来构建镜像

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
# 配置工作目录
WORKDIR /app
# 暴露容器端口
EXPOSE 80
EXPOSE 443
# 设置时区
ENV TZ = Asia/shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && $TZ > /etc/timezone #执行命令
# 复制文件到工作目录
COPY . .
#指定执行程序
ENTRYPOINT ["dotnet", "DockerAndK8s.dll"]
```

第三步，通过Docker命令进行构建镜像

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker5.png)

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker6.png)

第五步，启动镜像

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker7.png)

最后，在浏览器中访问可以发现成功部署

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker8.png)



### 将镜像发布到Docker Hub

通过命令行进行

```bash
F:\Visual Studio\repos\DockerAndK8s\output>docker tag dockerandk8s:v1 lesan0u0/dockerandk8s:v1
F:\Visual Studio\repos\DockerAndK8s\output>docker push lesan0u0/dockerandk8s:v1
```



## 添加Kubernetes支持

> Kubernetes的安装与使用通过本篇文章 [ASP.NET Core on K8S学习初探（1）K8S单节点环境搭建](https://www.cnblogs.com/edisonchou/p/aspnet_core_on_k8s_firststudy_part1.html)

第一步，打开控制面板

```
kubectl create -f kubernetes-dashboard.yaml // 部署Kubernetes dashboard
kubectl get pod -n kubernetes-dashboard // 检查 kubernetes-dashboard 应用状态
kubectl proxy // 开启 API Server 访问代理

//dashboard地址
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

// 控制台访问令牌
$TOKEN=((kubectl -n kube-system describe secret default | Select-String "token:") -split " +")[1]
kubectl config set-credentials docker-desktop --token="${TOKEN}"
echo $TOKEN
```



第二步，准备Department YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dockerandk8s
  namespace: test
  labels:
    name: dockerandk8s
spec:
  replicas: 2
  selector:
    matchLabels:
      name: dockerandk8s
  template:
    metadata:
      labels:
        name: dockerandk8s
    spec:
      containers:
      - name: dockerandk8s
        image: lesan0u0/dockerandk8s:v1
        ports:
        - containerPort: 80
        imagePullPolicy: Always

---

kind: Service
apiVersion: v1
metadata:
  name: dockerandk8s
  namespace: test
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
  selector:
    name: dockerandk8s
```



第三步，通过kubectl部署到K8S

```bash
kubectl create -f deploy.yaml  //部署
kubectl get svc -n aspnetcore  //验证
```

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker9.png)

可以通过Dashboard来查看部署情况

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker11.png)

最后，大功告成

![](https://cdn.jsdelivr.net/gh/LesanOuO/images/img/DotNet4Docker10.png)