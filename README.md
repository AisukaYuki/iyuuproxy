### docker容器本地自签证书反代iyuu使用例

容器互相访问，可不开放443端口。以免与其他应用冲突。
如需在局域网内其他设备使用，则需要开端口。
修改compose文件注释即可
```
cd /iyuuproxy
#运行iyuuproxy容器
docker-compose up -d
#查看ip
docker inspect iyuuproxy | grep IPAddress 
```
如ip为 **172.29.0.2**，修改iyuuplus的compose文件添加**extra_hosts**参数后重建,如：
```
version: '3.4'
   services:
     iyuuplus:
      container_name: iyuuplus
      image: iyuucn/iyuuplus:latest
      extra_hosts: 
        - "api.iyuu.cn:172.29.0.2"
      ports:
        - 8787:8787
      volumes:
        - ./db:/IYUU/db
      restart: unless-stopped
```
或修改容器内 /etc/hosts 添加`172.29.0.2 api.iyuu.cn`其他容器同理
```
docker exec -it <容器ID或容器名称> /bin/sh -c 'echo "172.29.0.2 api.iyuu.cn" >> /etc/hosts'
```
#### 一般来说，无需导入证书信任，添加完hosts，iyuu登录和mp认证即可正常工作。
##### 如果有有特殊情况无法工作，可尝试导入证书。
```
docker cp iyuu.crt <容器ID或容器名称>:/usr/local/share/ca-certificates/iyuu.crt
docker exec -it <容器ID或容器名称> /bin/sh -c ‘update-ca-certificates'
```

#### 恢复则修改compose或hosts文件，删除添加的内容，重建或重启。