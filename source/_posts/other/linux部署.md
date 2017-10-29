---
title: linux部署
type: "categories"
categories: 其他
---

# 部署node
## Ubuntu内核
```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```

sudo apt-get install -y nodejs
## 非Ubuntu内核
On RHEL, CentOS or Fedora, for Node.js v6 LTS:
```
curl --silent --location https://rpm.nodesource.com/setup_6.x | sudo bash -
```
Alternatively for Node.js 8:
```
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
```
Then install:
```
sudo yum -y install nodejs
```

# 如何在后台启动服务
linux下， 使用yum install mongodb-server及service mongod start，可以启动，但mongodb默认属于低版本，启动的mongodb配置文件需另外指定。

# mongod 安装
```
curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.0.6.tgz    # 下载
tar -zxvf mongodb-linux-x86_64-3.0.6.tgz                                   # 解压

mv  mongodb-linux-x86_64-3.0.6/ /usr/local/mongodb 

MongoDB 的可执行文件位于 bin 目录下，所以可以将其添加到 PATH 路径中：
export PATH=<mongodb-install-directory>/bin:$PATH
<mongodb-install-directory> 为你 MongoDB 的安装路径。如本文的 /usr/local/mongodb
```
# 创建数据库目录

mkdir -p /data/db
./mongod --dbpath=/data/db --rest

# MongoDB后台管理 Shell
```
$ cd /usr/local/mongodb/bin
$ ./mongo
```

# pm2
pm2 start app.js  用pm2来守护进程


# nginx 转发
linux下路径： /etc/nginx/nginx.conf, 可通过 include /etc/nginx/default.conf， 新建个文件自定义配置。
以下是部署到同一个域名下的案例
```
server {
        listen       80;
        server_name  xxxx.alpha.elenet.me; //外网域名
        location / {
                root /xcy/;
                try_files $uri $uri/ /index.html;
        }
        location /api/ {
                proxy_buffer_size 64k;
                proxy_buffers   32 32k;
                proxy_busy_buffers_size 128k;
                proxy_set_header   X-Real-IP            $remote_addr;
                proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
                proxy_set_header   Host                   $http_host;
                proxy_set_header   X-NginX-Proxy    true;
                proxy_set_header   Connection "";
                proxy_http_version 1.1;
                proxy_pass   http://localhost:8080/api/;
        }
}
```