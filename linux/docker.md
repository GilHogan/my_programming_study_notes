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