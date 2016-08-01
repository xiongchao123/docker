### 负载均衡配置测试


```sh
docker run --name nginxslave1 -d nginx
docker cp ./upstream/slave1.conf nginxslave1:/etc/nginx/conf.d/
```


```sh
docker run --name nginxslave2 -d nginx
docker cp ./upstream/slave2.conf nginxslave2:/etc/nginx/conf.d/
```

```sh
docker run --name nginxmaster -p 8080:80 --link slave2:slave2 --link slave2:slave2 -d nginx
```