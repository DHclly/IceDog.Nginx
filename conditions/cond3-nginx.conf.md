# 场景定义

## 要求

配置站点 site1.master

要求

- 正常访问site1.master
- site1.master使用端口 7110
- 可通过 url : http://localhost:7110/ 访问
- 通过 http://localhost:7110/images/ 访问 /www/IceDog.Nginx/wwwroot/common/images 文件夹
- 通过 http://localhost:7110/face/ 访问 /www/IceDog.Nginx/wwwroot/common/images/face文件夹
- 通过 http://localhost:7110/css/ 访问 /www/IceDog.Nginx/wwwroot/common/css/文件夹
- 通过 http://localhost:7110/docs/ 访问 /www/IceDog.Nginx/wwwroot/docs文件夹,而且是目录浏览形式，需要兼容中文文件名


## 配置

server 段

```
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
```

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

  # 服务模块
  server{
    # 监听端口 7110 
    listen 7110 default;

    #默认服务名，在linux下可以使用localhost，在win下使用
    #127.0.0.1最好，不然在反向代理会出问题，加载很慢，win下专属bug
    server_name 127.0.0.1;

    # 定位 / (根目录开头) 的请求
    location / {
      
      # 根目录
      root /www/IceDog.Nginx/wwwroot/site1.master;

      # 默认页
      index index.html;
    }

    location /images/ {
      # 设置根目录，实际访问路径等于根路径+location定位的路径
      # 访问实际文件的路径是/www/IceDog.Nginx/wwwroot/common/images/
      root /www/IceDog.Nginx/wwwroot/common;
    }
    location /face/ {
      # 根目录
      root /www/IceDog.Nginx/wwwroot/common/images;
    }
    location /css/ {
      # 使用别名
      # location定位的路径访问的实际路径就是alias指定的路径
      # 尾部要加 /
      alias /www/IceDog.Nginx/wwwroot/common/css/;
    }
    location /docs/ {
      # 使用别名
      # location定位的路径访问的实际路径就是alias指定的路径
      alias /www/IceDog.Nginx/wwwroot/docs/;
      autoindex on;
      autoindex_exact_size on;
      autoindex_localtime on;
      charset utf-8;
    }
  }
}
```
