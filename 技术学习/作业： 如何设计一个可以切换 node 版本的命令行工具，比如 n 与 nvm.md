# 作业： 如何设计一个可以切换 node 版本的命令行工具，比如 n 与 nvm
作业： 如何设计一个可以切换 node 版本的命令行工具，比如 n 与 nvm  
说一下 nvm 的原理吧  
nvm 的常用命令  
nvm ls 列举当前目录下的所有 node 版本  
nvm install xxx 安装某个版本 node  
nvm install 命令 下载 node ：会把对应版本的 node 下载到 nvm 目录下 保存起来  
which node  
/Users/yinzhuo/.nvm/versions/node/v14.19.1/bin/node  
nvm use xxx 使用某个版本 node  
会把 node 版本 软链接的地址 切换到 NVM 目录下对应版本的 node 目录  
 echo $path  
/Users/yinzhuo/.nvm/versions/node/v14.19.1/bin /opt/homebrew/bin /opt/homebrew/sbin /usr/local/bin /usr/bin /bin /usr/sbin /sbin  
每次去取环境变量的时候，会去优先去取.nvm/versions/node 下的包

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/ik11ys