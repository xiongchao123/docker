## Kubernetes 指南
容器可以配置为非常容易地在kubernetes中运行，您可以利用该 ```kubectl exec``` 命令来运行pull和push脚本，以便在github进行更改时同步。该指南假设您有一个工作的kubernetes设置和kubectl正在工作。

下面的配置是一个如何快速运行的例子。

### 配置应用程序

在这个例子中，我们将为自己的命名空间部署一个示例应用程序，以方便分离。创建以下 ```example-namespace.yml``` 文件:

```
apiVersion: v1
kind: Namespace
metadata:
  name: example
```

现在在kubernetes中创建命名空间:

```
kubectl create -f example-namespace.yml
```

创建以下内容 ```example-app.yml```, ，这是实际创建容器和复制控制器的位，引用Docker映像和github凭据。

```
apiVersion: v1
kind: ReplicationController
metadata:
  namespace: example
  name: example-app
  labels:
    example-component: example-app
spec:
  replicas: 1
  selector:
    example-component: example-app
  template:
    metadata:
      labels:
        example-component: example-app
    spec:
      containers:
      - name: example-app
        image: richarvey/nginx-php-fpm:latest
        imagePullPolicy: Always
        env:
          - name: SSH_KEY
            value: '<YOUR_KEY_HERE>'
          - name: GIT_REPO
            value: 'git@gitlab.com:<YOUR_USER>/<YOUR_REPO>.git'
          - name: GIT_EMAIL
            value: '<YOUR_EMAIL>'
          - name: GIT_NAME
            value: '<YOUR_NAME>'
        ports:
        - containerPort: 80
```
现在运行：:

```
kubectl create -f example-app.yml
```

### 使用应用程序

您的容器现在应该正常运行，您可以使用以下命令查看其详细信息：

```
kubectl get pods --namespace example

# make a note of the pod namespace

kubectl describe pod <pod_name> --namespace example
```

### 为应用程序创建一个服务

为了帮助公开应用程序到外界，您可能需要创建一个服务。下面的示例不是唯一的方法，因为它取决于您具有的kubernetes系统的确切设置，例如您可能希望在AWS上使用ELB，或者您可以在GKE上使用Google Googles http负载均衡器。

创建 ```example-service.yml``` 具有以下内容的文件:

```
apiVersion: v1
kind: Service
metadata:
  namespace: example
  name: example-app
  spec:
  type: ClusterIP
  ports:
    - protocol: TCP
      name: http
      port: 80
      targetPort: 80
  selector:
    app: example-app
```
现在运行：
```
kubectl create -f example-service.yml
```
这将为您创建一个服务负载平衡器，并允许您在复制控制器的统一IP地址下的后台扩展。您可以通过运行以获得详细信息：


```
kubectl describe service example-app --namespace example
```
### 在容器/ pod中运行命令
如果要将代码推或拉到容器，可以运行以下命令：
```
kubectl get pods --namespace example

# make a note of the pod namespace

# update code in the container
kubectl exec -t <pod_name> /usr/bin/pull --namespace example
# push code back to github
kubectl exec -t <pod_name> /usr/bin/push --namespace example
```
如果你想放入shell运行以下命令：
```
kubectl exec -it <pod_name> bash --namespace example
```

### 缩放你的应用程序
您可以使用以下命令缩放复制控制器：
```
kubectl scale --replicas=3 rc/example-app --namespace example
```
