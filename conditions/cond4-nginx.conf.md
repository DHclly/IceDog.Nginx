# 场景定义

## 要求

配置一个代理服务器

**条件**

- 代理服务器端口为7500，可通过 url : http://localhost:7500/ 访问
- 被代理的站点有site1.master,端口是7110 ，可通过url : http://localhost:7110/ 访问
- 被代理的站点有site1.slave1,端口是7111 ，可通过url : http://localhost:7111/ 访问
- 被代理的站点有site1.slave2,端口是7112 ，可通过url : http://localhost:7112/ 访问
- 三个站点被代理到同一个端口 7500,做负载均衡，以轮询方式负载(还没搞定)

## 配置

完整版

```nginx
# 工作进程设置为一个
worker_processes 1 ;

# pid 进程id存储到的文件位置
pid logs/nginx.pid ;

# 事件模块，必须加上，为空使用默认配置
events 
{
  # 工作进程最大连接数
  worker_connections  1024;
}

# http 模块
http{

  # 需要加上这个，否则浏览器虽然会获取到文件，但是解析方式会
  # 不是预期的方式，比如css文件不会渲染
  include mime.types;

  # 默认文件类型
  default_type application/octet-stream;

  # keepalive 超时时间 65秒
  keepalive_timeout  65;
  
  # 被代理服务器
  server{
    listen 7110 default;
    server_name 127.0.0.1;

    location / {
      root /www/IceDog.Nginx/wwwroot/site1.master;
      index index.html;
    }

    location /images/ {
      root /www/IceDog.Nginx/wwwroot/common;
    }
    location /face/ {
      root /www/IceDog.Nginx/wwwroot/common/images;
    }
    location /css/ {
      alias /www/IceDog.Nginx/wwwroot/common/css/;
    }
    location /docs/ {
      alias /www/IceDog.Nginx/wwwroot/docs/;
      autoindex on;
      autoindex_exact_size on;
      autoindex_localtime on;
      charset utf-8;
    }
  }
  server{
    listen 7111 default;
    server_name 127.0.0.1;

    location / {
      root /www/IceDog.Nginx/wwwroot/site1.slave1;
      index index.html;
    }

    location /images/ {
      root /www/IceDog.Nginx/wwwroot/common;
    }
    location /face/ {
      root /www/IceDog.Nginx/wwwroot/common/images;
    }
    location /css/ {
      alias /www/IceDog.Nginx/wwwroot/common/css/;
    }
    location /docs/ {
      alias /www/IceDog.Nginx/wwwroot/docs/;
      autoindex on;
      autoindex_exact_size on;
      autoindex_localtime on;
      charset utf-8;
    }
  }

  server{
    listen 7112 default;
    server_name 127.0.0.1;

    location / {
      root /www/IceDog.Nginx/wwwroot/site1.slave2;
      index index.html;
    }

    location /images/ {
      root /www/IceDog.Nginx/wwwroot/common;
    }
    location /face/ {
      root /www/IceDog.Nginx/wwwroot/common/images;
    }
    location /css/ {
      alias /www/IceDog.Nginx/wwwroot/common/css/;
    }
    location /docs/ {
      alias /www/IceDog.Nginx/wwwroot/docs/;
      autoindex on;
      autoindex_exact_size on;
      autoindex_localtime on;
      charset utf-8;
    }
  }

  upstream site1{
    server 127.0.0.1:7110 weight=3;
    server 127.0.0.1:7111 weight=2;
    server 127.0.0.1:7112 weight=1;
  }
  
  server{
    listen 7500 default;
    server_name 127.0.0.1;
    location / {
      proxy_pass http://site1;
    }
  }
}
```
