## Git命令
指定 ```GIT_EMAIL``` 和 ```GIT_NAME``` 变量就可以运行. 它们用来正确设置git，并允许以下命令工作。

### 将代码推送到Git
将容器内的代码更改推回到git运行
```
sudo docker exec -t -i <CONTAINER_NAME> /usr/bin/push
```
### 从Git（刷新）提取代码
为了刷新容器中的代码，并从git运行更新代码：
```
sudo docker exec -t -i <CONTAINER_NAME> /usr/bin/pull
```
