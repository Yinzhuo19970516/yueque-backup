# day22 函数
## 作业

1. [nodejs 官方镜像的 docker-entrypoint](https://github.com/nodejs/docker-node/blob/main/16/alpine3.16/docker-entrypoint.sh) 是何意思？

```javascript
#!/bin/sh
set -e

# Run command with node if the first argument contains a "-" or is not a system command. The last
# part inside the "{}" is a workaround for the following bug in ash/dash:
# https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=874264
if [ "${1#-}" != "${1}" ] || [ -z "$(command -v "${1}")" ] || { [ -f "${1}" ] && ! [ -x "${1}" ]; }; then
  set -- node "$@"
fi

exec "$@"
```

满足三个条件之一  
[ "${1#-}" != "${1}" ] 第一个参数包含 -  
[ -z "$(command -v "${1}")" ]第一个参数不是系统指令  
 { [ -f "${1}" ] && ! [ -x "${1}" ]; } 第一个参数 是文件，且不可执行  
把所有参数作为 node 的执行参数，并执行  
set -- 后有内容，当前 shell 脚本的参数被替换为 set -- 后的内容，$1 $? $@ 等相应地被改变

2. $0$1$@ 各代表什么意思

$0 函数名  
$1 第一个参数  
$@ 所有参数

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/ikp123