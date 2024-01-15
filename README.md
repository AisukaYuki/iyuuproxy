## docker容器本地自签证书反代iyuu使用例

容器互相访问，可不开放443端口。以免与其他应用冲突。

如需在局域网内其他设备使用，如macvlan容器、win电脑等，则需要开端口。修改compose文件注释即可。

```
git clone https://github.com/AisukaYuki/iyuuproxy.git
cd ./iyuuproxy
#运行iyuuproxy容器
docker-compose up -d
#查看ip命令，也可在各docker管理器查看
docker inspect iyuuproxy | grep IPAddress 
```
```
shell输出：
 "SecondaryIPAddresses": null,
 "IPAddress": "",
    "IPAddress": "172.29.0.2",
```
如上ip为 **172.29.0.2**，测试是否生效：
##### 注：如果开启了443端口映射，则可以直接使用宿主ip。
```
curl -k --resolve 'api.iyuu.cn:443:172.29.0.2' https://api.iyuu.cn

#返回json内容则成功
{"ret":200,"data":{"title":"Welcome IYUU","time":1705208847,"ip":"192.168.0.1","userAgent":"curl\/7.81.0","group":"QQ群：859882209, 931954050, 924099912, 586608623"},"msg":"","version":"2.0.0"}
```
##### 使用方法：
修改iyuuplus的compose文件添加**extra_hosts**参数后重建,如：
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
或修改容器内 /etc/hosts 添加`172.29.0.2 api.iyuu.cn`
```
docker exec -it <容器ID或容器名称> /bin/sh -c 'echo "172.29.0.2 api.iyuu.cn" >> /etc/hosts'
#此方法容器重启后需要重新执行，建议添加外部挂载文件替换容器的/etc/hosts。重启容器不会失效。
```

当然你也可以直接修改宿主hosts文件，总之方法很多。自己选

#### 其他容器操作同理

#### 一般来说，无需导入证书信任，添加完hosts，iyuu登录和mp认证即可正常工作。
##### 如果有特殊情况无法工作，可尝试导入证书，并添加信任。
```
docker cp ./iyuu.crt <容器ID或容器名称>:/usr/local/share/ca-certificates/iyuu.crt
docker exec -it <容器ID或容器名称> /bin/sh -c 'update-ca-certificates'
```

#### 恢复则修改compose或hosts文件，删除添加的内容，重建或重启。
