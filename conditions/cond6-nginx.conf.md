# 场景定义

## 要求

配置站点 site3

**条件**

- 正常访问site0
- 端口是 7130
- 可通过url: http://localhost:7130/ 访问
- site3的目录结构和文件位置和其他的一样，不变
- 页面中访问js的路径是 /js/index.js
- 页面中访问img的路径是 /img/index.jpg
- 页面中访问css的路径是 /css/index.css
- 使用rewrite实现
- 使页面正常访问

## 配置

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
    # 监听端口 7130 
    listen 7130 default;

    #默认服务名，在linux下可以使用localhost，在win下使用
    #127.0.0.1最好，不然在反向代理会出问题，加载很慢，win下专属bug
    server_name 127.0.0.1;
    # 这样下面没有设置root的location，都会使用此root
    root /www/IceDog.Nginx/wwwroot/site3;
    # 定位 / (根目录开头) 的请求
    location / {
      index index.html;
    }
    location ~* /css/ {
      rewrite (?i)/css/(.*) /$1 break;
      return 404;
    }
    location ~* /js/ {
      rewrite (?i)/js/(.*) /$1 break;
      return 404;
    }
    location ~* /img/ {
      # (?i)正则匹配不区分大小写
      rewrite (?i)/img/(.*) /$1 break;
      return 404;
    }
  }
}
```
