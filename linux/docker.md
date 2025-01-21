### Dockerfile 接收docker run命令的动态参数配置示例
```conf
FROM ubuntu:22.04

# ARG 设置默认值
ARG custom_memory=1
ENV custom_memory ${custom_memory}

VOLUME /tmp
COPY NeverIdle NeverIdle

CMD ./NeverIdle -c 12h -m ${custom_memory} -n 12h

# docker build 示例: docker build -t keep_vps_alive .
# docker run 示例: docker run -d --name keepVpsAlive --restart always -e custom_memory=2 keep_vps_alive
```

### docker mysql 安装
```conf
docker run --name mysql8 -p 3307:3306 --restart=unless-stopped -v /etc/mysql8/data:/var/lib/mysql -v /etc/mysql8/conf.d:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --lower-case-table-names=1
```

### docker nginx-http3 安装
```conf
docker run --restart unless-stopped --cap-add=NET_BIND_SERVICE --network host --user root \
  -v "/web/path":/web/path:ro \
  -v "/etc/nginx/main.d/":/etc/nginx/main.d/:ro \
  -v "/etc/nginx/conf.d/":/etc/nginx/conf.d/:ro \
  -v "/etc/nginx/nginx.conf":/etc/nginx/nginx.conf:ro \
  --name nginx \
  -t macbre/nginx-http3
```
