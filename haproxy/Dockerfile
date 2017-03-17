# This dockerfile uses haproxy image
# VERSION 1.0
# Author: zhaojianhui
# Command format: Instruction [arguments / command] ..

FROM haproxy

# 更新类库
RUN apt-get update && apt-get upgrade -y

COPY haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg