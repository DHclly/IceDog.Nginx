# 场景定义

## 要求

配置一个代理服务器

**条件**

- 代理服务器端口为7500，可通过 url : http://localhost:7500/ 访问
- 被代理的站点是 site2,端口是7120 ，可通过url : http://localhost:7120/ 访问
- 通过http://localhost:7120/ 访问，不会有图片显示
- 图片访问在代理服务处配置，匹配所有结尾是gif png jpg 的图片，然后通过http://127.0.0.1:7500/o_ganbei.png 就可访问图片
- http://127.0.0.1:7500/打开界面正常，图片显示正常

## 配置

server 段

```
# (服务处理)
server{
  listen 7120 default;
  server_name 127.0.0.1;

  root /www/IceDog.Nginx/wwwroot/site2;
  index index.html;
  location / {

  }
}

# 代理配置，数据转发
server{
  # 监听端口 7500
  listen 7500 default;

  #默认服务名，在linux下可以使用localhost，在win下使用
  #127.0.0.1最好，不然在反向代理会出问题，加载很慢，win下专属bug
  server_name 127.0.0.1;
  # 根目录
  root /www/IceDog.Nginx/wwwroot/proxy;

  location / {
    #代理服务，在linux下可以使用localhost，在win下使用
    #127.0.0.1，不然加载很慢，win下专属bug
    proxy_pass http://127.0.0.1:7120/;
  }

  # 所有结尾是 .gif .jpg .png 的从这里读取
  location ~ \.(gif|jpg|png)$ {
      root /www/IceDog.Nginx/wwwroot/common/images/face;
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
  
  # (服务处理)
  server{
    listen 7120 default;
    server_name 127.0.0.1;

    root /www/IceDog.Nginx/wwwroot/site2;
    index index.html;
    location / {

    }
  }

  # 代理配置，数据转发
  server{
    # 监听端口 7500
    listen 7500 default;

    #默认服务名，在linux下可以使用localhost，在win下使用
    #127.0.0.1最好，不然在反向代理会出问题，加载很慢，win下专属bug
    server_name 127.0.0.1;
    # 根目录
    root /www/IceDog.Nginx/wwwroot/proxy;

    location / {
      #代理服务，在linux下可以使用localhost，在win下使用
      #127.0.0.1，不然加载很慢，win下专属bug
      proxy_pass http://127.0.0.1:7120/;
    }

    # 所有结尾是 .gif .jpg .png 的从这里读取
    location ~ \.(gif|jpg|png)$ {
        root /www/IceDog.Nginx/wwwroot/common/images/face;
    }
  }
}
```
