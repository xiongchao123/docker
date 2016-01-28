准备工作：
docker pull mysql
docker pull php:7.0.2-fpm
docker pull nginx


docker run --name mysql --volumes-from mysqldata -e MYSQL_ROOT_PASSWORD=123456 -d mysql