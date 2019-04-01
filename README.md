# IceDog.Nginx

## 介绍

对nginx的配置的练习

前提是有装nginx ，windows 或者 linux（推荐）平台皆可，不过在windows平台下需要注意

server_name不要使用localhost，使用127.0.0.1，否则在反向代理下，相关资源加载特别慢，应该是bug，linux下不存在。

/www/IceDog.Nginx/wwwroot

nginx -h

nginx -v

nginx -s reload