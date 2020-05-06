# springboot-fastdfs-example

docker for mac 环境下定制化docker-compose.yml

docker-compose.yml
```yaml
version: '3.6'

services:
  tracker:
    container_name: tracker
    image: ygqygq2/fastdfs-nginx:latest
    command: tracker
    restart: always
    networks:
      fdfs-net:
        ipv4_address: 172.24.0.2 # 设置tracker服务器固定 ip，该 ip 必须在下文创建的网络的网段范围之内
    volumes:
      - ./fdfs/tracker:/var/fdfs
  
  storage:
    container_name: storage
    image: ygqygq2/fastdfs-nginx:latest
    command: storage
    restart: always
    depends_on:
      - tracker
    networks:
      fdfs-net:
        ipv4_address: 172.24.0.3 # 设置storage服务器固定 ip，该 ip 必须在下文创建的网络的网段范围之内
    environment:
      - TRACKER_SERVER=172.24.0.2:22122 # 设置tracker服务器地址和端口
    volumes:
      - ./fdfs/storage:/var/fdfs

# 重新定义网络
networks:
  fdfs-net:
    name: fdfs-net # 自定义网络名称
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.24.0.0/24 # 定义网段
```

配合 https://github.com/ameizi/docker-mac-network 更香噢，docker for mac专属定制。解决宿主机不能访问容器的痛点。

注意：

```
容器内访问宿主机，在 Docker 18.03 过后推荐使用 特殊的 DNS 记录 host.docker.internal 访问宿主机。
但是注意，这个只是在 Docker Desktop for Mac 中作为开发时有效。 
网关的 DNS 记录: gateway.docker.internal。
```

# 参考文档

使用的 docker 镜像

https://github.com/ygqygq2/fastdfs-nginx

docker for mac 宿主机和容器通信方案

https://github.com/wojas/docker-mac-network

https://github.com/pengsrc/docker-for-mac-kubernetes-devkit

https://yuanmomo.net/2019/06/13/docker-network

https://my.oschina.net/binny/blog/3062250

https://pjw.io/articles/2018/04/25/access-to-the-container-network-of-docker-for-mac

docker compose文档

https://docs.docker.com/compose/compose-file/