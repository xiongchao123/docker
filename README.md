# docker
dockerѧϰ����������

###docker������
Docker Hub �ڹ��⣬��ʱ����ȡ Image ���仺��������ʹ�ù��ڵľ�����ʵ�ּ���

####������
```sh
echo "DOCKER_OPTS=\"--registry-mirror=https://yourlocation.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
sudo service docker restart
```
> ���� https://yourlocation.mirror.aliyuncs.com �����ڰ�����ע����ר����������ַ
[�ĵ���ַ](https://yq.aliyun.com/articles/29941)

#### DaoCloud
```sh
sudo echo ��DOCKER_OPTS=\��\$DOCKER_OPTS �Cregistry-mirror=http://your-id.m.daocloud.io -d\���� >> /etc/default/docker
sudo service docker restart
```
> ���� http://your-id.m.daocloud.io ������ DaoCloud ע����ר����������ַ��
[�ĵ���ַ](https://www.daocloud.io/)

###�ֶ����ü�����
ԭ�����ҵ����������ļ��е�ExecStart��һ�У�Ȼ�������������������registry-mirror�������л������𵽼��ٵ�Ŀ�ġ����ַ�ʽ��
> �����ƣ�https://qqe07tk2.mirror.aliyuncs.com
> 
> DaoCloud: http://0752ec30.m.daocloud.io

����ִ�У�
```sh
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://0752ec30.m.daocloud.io
```

�ֶ�ִ�У�
```sh
sudo cp -bf /lib/systemd/system/docker.service /etc/systemd/system/docker.service
sudo sed -i "s|ExecStart=/usr/bin/dockerd|ExecStart=/usr/bin/dockerd --registry-mirror=https://qqe07tk2.mirror.aliyuncs.com|g" /etc/systemd/system/docker.service
sudo systemctl daemon-reload
sudo service docker restart
```

####����ɾ������
��runһ�£� docker images | grep "^" | awk "{print $3}"�� �����ǲ���ȷʵ��  ��image�����û�У�ֱ�����л��������ڵ����⡣
```sh
docker rmi $(docker images | grep "^" | awk "{print $3}")
```

�ٷ��ֿ��ַ��
> php           
> [Ӣ�ĵ�ַ](https://hub.docker.com/_/php/)              
> [���ĵ�ַ](https://github.com/DaoCloud/library-image/tree/master/php)
> <br>
> mysql     
> [Ӣ�ĵ�ַ](https://hub.docker.com/_/mysql/)         
> [���ĵ�ַ](https://github.com/DaoCloud/library-image/tree/master/mysql)
> <br>
> nginx       
> [Ӣ�ĵ�ַ](https://hub.docker.com/_/nginx/)          
> [���ĵ�ַ](https://github.com/DaoCloud/library-image/tree/master/nginx)
> 
> <br>
> redis       
> [Ӣ�ĵ�ַ](https://hub.docker.com/_/redis/)
> 
> <br>
> memcached       
> [Ӣ�ĵ�ַ](https://hub.docker.com/_/memcached/)
> 

####����mysql���񲿷�
```sh
docker build -t=zhaojianhui129/mysql:latest ./mysql/
```
����MYSQL������
```sh
docker run --name mysql -v /data/mysql:/var/lib/mysql -p 3306:3306 -d zhaojianhui129/mysql:latest
```
//�ٷ�ԭ��������5.7��
```sh
docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=test -e MYSQL_USER=qianxun -e MYSQL_PASSWORD=123456 -v /data/mysql:/var/lib/mysql -p 3306:3306 -d mysql
```

//�ٷ�ԭ��������5.6��
```sh
docker run --name mysql56 -e MYSQL_ROOT_PASSWORD=123456 -e MYSQL_DATABASE=test -e MYSQL_USER=qianxun -e MYSQL_PASSWORD=123456 -v /data/mysql56:/var/lib/mysql -p 3307:3306 -d mysql:5.6
```

�½���������Ա�˺Ų鿴Dockerfiles��Ϣ

####����memcached�����洢session�������˿�11211�������˿�11211
```sh
docker run --name memcached -p 11211:11211 -d memcached
```

###����redis����,����6379�˿ڣ�����6379
```sh
docker run --name redis -d -v /data/redis:/data -p 6379:6379 redis redis-server --appendonly yes
```

####����һ������Ŀ¼��Ϊ�������ݾ�����
```sh
#�Լ��Զ�����ģ���Ϊubuntu����Ƚ�С
docker run -d -v /mnt/hgfs/website/:/www-data/ --name web ubuntu echo Data-only container for postgres
#�ֲ����½����ݾ�����
docker run -d -v /mnt/hgfs/GIT/:/www-data/ --name web training/postgres echo Data-only container for postgres
```


####����php���񲿷�
> ��Ч��php��չ�б�Ϊ:
> bcmath bz2 calendar ctype curl dba dom enchant exif fileinfo filter ftp gd gettext gmp hash iconv imap interbase intl json ldap mbstring mcrypt mysqli oci8 odbc opcache pcntl pdo pdo_dblib pdo_firebird pdo_mysql pdo_oci pdo_odbc pdo_pgsql pdo_sqlite pgsql phar posix pspell readline recode reflection session shmop simplexml snmp soap sockets spl standard sysvmsg sysvsem sysvshm tidy tokenizer wddx xml xmlreader xmlrpc xmlwriter xsl zip

```sh
#php7
docker build -t=zhaojianhui129/php:fpm ./php7fpm/
docker build -t=zhaojianhui129/php:cli ./php7cli/
#php5
docker build -t=zhaojianhui129/php:5-fpm ./php5fpm/
```

����php����
����������Ŀ¼��ʽ�������Ƽ�
```sh
docker run --name php -v /mnt/hgfs/GIT/:/www-data/ -d zhaojianhui/lnmp:php
```
���������ݾ�������ʽ���Ƽ���
```sh
docker run --name php --volumes-from web -p 9000:9000 -d zhaojianhui129/php:fpm
docker run --name php5 --volumes-from web -p 9001:9000 -d zhaojianhui129/php:5-fpm

#cliģʽ
docker run -it --rm --name phpcli -v /mnt/hgfs/GIT/swooletest/:/data/swooletest/ -w /data/swooletest/ -p 9503:9503 zhaojianhui129/php:cli php timerTick.php

# memcached�����洢session
docker run --name php5 --volumes-from web --link memcached:memcached -d zhaojianhui/lnmp:php5
```


####����nginx���񲿷�
ʹ��docker-ip�鿴php������ip��ַ��Ȼ�����ú�./nginx/vhostsĿ¼�µ������ļ���Ȼ�������Զ�������������;
> ע��nginx�����ļ���fastcgi_pass�������֣��Ѿ���Ϊ��php�������ƣ�ʹ�ô˷������Բ��õ�������ip��ʱ�л������⡣
> ��Ҫע����ǣ����ǽ�fastcgi_pass��ֵ��127.0.0.1:9000��Ϊ��phpfpm:9000�������phpfpm����������nginx������/etc/hosts�ļ����Զ�����Ϊphpfpm�����ķ���IP��

```sh
docker build -t=zhaojianhui129/nginx:latest ./nginx/
```

######����nginx������
��php�����Ĺ���Ŀ¼Ϊ׼������ͬ����Ŀ¼,ʹ�����������ķ�ʽ������������IP��仯��
```sh
docker run --name nginx --volumes-from web  -p 80:80 -d zhaojianhui129/nginx:latest
```
�Զ������Ŀ¼����php�Ĺ���Ŀ¼����һ�£��˷�ʽû�й������ݾ�������
```sh
docker run --name nginx -v /mnt/hgfs/GIT/:/www-data/  -p 80:80 -d zhaojianhui129/nginx:latest
```


####�ύ����Զ�ֿ̲�
```sh
docker push zhaojianhui129/php:fpm

```