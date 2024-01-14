在iyuuproxy所在目录运行 docker-compose up -d 等待iyuuproxy运行

执行 docker inspect iyuuproxy | grep IPAddress 查看ip

例如ip为 172.29.0.2，修改iyuu的compose文件内ip，然后运行docker-compose up -d 运行或重建iyuu

或修改容器内 /etc/hosts 添加 172.29.0.2 api.iyuu.cn

其他容器同理都改hosts文件或compose文件添加如下参数后重建
```
    extra_hosts: 
      - "api.iyuu.cn:172.29.0.2"
