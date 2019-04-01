# 场景定义

## 要求

配置站点 site 0 

**要求**

- 正常访问site0
- 端口是 7100
- 可通过url: http://localhost:7100/ 访问

## 配置

server 段

```
  server{
    listen 7100 default;
    server_name 127.0.0.1;
    location / {
      root /www/IceDog.Nginx/wwwroot/site0;
      index index.html;
    }
  }
```

完整

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

  # 服务模块
  server{
    # 监听端口 7100 
    listen 7100 default;

    #默认服务名，在linux下可以使用localhost，在win下使用
    #127.0.0.1最好，不然在反向代理会出问题，加载很慢，win下专属bug
    server_name 127.0.0.1;

    # 定位 / (根目录开头) 的请求
    location / {
      
      # 根目录
      root /www/IceDog.Nginx/wwwroot/site0;

      # 默认页
      index index.html;
    }
  }
}
```
