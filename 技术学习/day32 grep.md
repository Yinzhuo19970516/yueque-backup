# day32 grep
grep  
命令行中使用正则表达式对文件进行匹配筛选。

## grep -r

在目录中进行搜索，可使用 -r 参数。

## grep -v

根据正则反向匹配。

## 作业

1. 如何匹配以 HELLO 开头并以 WORLD 结尾的行  
   grep '^HELLO.\*.WORLD$' test.js
2. 如何匹配以 HELLO 开头或以 WORLD 结尾的行  
   grep '^HELLO\|WORLD$' test.js

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/qx2250