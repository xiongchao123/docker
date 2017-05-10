### 负载均衡配置测试


```sh
docker run --name nginxslave1 -d nginx
docker cp ./upstream/slave1.conf nginxslave1:/etc/nginx/conf.d/
cd ..
echo "<h1>服务器一</h1>" > ./index.html;
docker cp ./index.html nginxslave1:/usr/share/nginx/html
docker restart nginxslave1
```


```sh
docker run --name nginxslave2 -d nginx
docker cp ./upstream/slave2.conf nginxslave2:/etc/nginx/conf.d/
echo "<h1>服务器二</h1>" > ./index.html;
docker cp ./index.html nginxslave2:/usr/share/nginx/html
docker restart nginxslave2
```

```sh
docker run --name nginxmaster -p 8080:80 --link nginxslave1:nginxslave1 --link nginxslave2:nginxslave2 -d nginx
docker cp ./upstream/master.conf nginxmaster:/etc/nginx/conf.d/
echo "<h1>主服务器</h1>" > ./index.html;
docker cp ./index.html nginxmaster:/usr/share/nginx/html
docker restart nginxmaster
```