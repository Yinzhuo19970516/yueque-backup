# ngnix
brew info nginx  
查看安装信息  
 /opt/homebrew/etc/nginx/nginx.conf  
查看配置  
open /opt/homebrew/opt/nginx  
查看安装目录  
open /opt/homebrew/var/www  
查看打开配置目录  
brew services start nginx  
brew services stop nginx  
brew services restart nginx  
启动、停止、重启 nginx(推荐)  
ps aux|grep nginx

查询 nginx 进程  
cd /usr/local/nginx

## nginx 三大作用

- 静态资源匹配
  - 四种 location 语法
  - 优先级是 精确匹配（=） > 高优先级前缀匹配（^~） > 正则匹配（～ ~\*） > 普通前缀匹配
- 动态资源反向代理
  - nginx 反向代理可以修改请求、响应信息，比如设置 header
- 负载均衡
  - 可以通过 upstream 配置负载均衡，有 4 种策略：轮询、带权重的轮询、ip_hash、fair。
  - 轮询：默认方式。
  - weight：在轮询基础上增加权重，也就是轮询到的几率不同。
  - ip_hash：按照 ip 的 hash 分配，保证每个访客的请求固定访问一个服务器，解决 session 问题。
  - fair：按照响应时间来分配，这个需要安装 nginx-upstream-fair 插件。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1689217436890-724e1fef-28ff-44d2-b93d-36a9ee31aca3.png#averageHue=%23242120&clientId=ueddc2f95-9dae-4&from=paste&height=603&id=u02f18a99&originHeight=1206&originWidth=912&originalType=binary&ratio=2&rotation=0&showTitle=false&size=168254&status=done&style=none&taskId=uc352d9ca-3f60-4c51-bfa5-0a0e5d79f2d&title=&width=456)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/gvm8bk