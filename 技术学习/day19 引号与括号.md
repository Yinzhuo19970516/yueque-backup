# day19 引号与括号
## 引号

反引号：直接执行命令  
双引号：对变量名替换输出  
单引号：原样输出  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658970504453-3213de30-7806-44dc-859e-44a904a0b8b4.png#clientId=u1108419e-f51b-4&from=paste&height=95&id=ud2ca1d13&name=image.png&originHeight=190&originWidth=734&originalType=binary&ratio=1&rotation=0&showTitle=false&size=50839&status=done&style=none&taskId=u7fc4280e-84d1-405b-807b-692d61a6cdf&title=&width=367)

## 小括号

$() = ``  
有点模版字符串那味了，我感觉是 es6 借鉴了 linux 借鉴了这里

## 中括号

[[]] 布尔运算符，返回 true 或者 false。

## 作业

1. shell 中 ${} 与 ${} 有什么区别
2. shell 中 ' 与 " 有什么区别

直接返回 运算执行后返回

3. shell 中 [[]] 与 (()) 有什么区别

返回布尔运算符 返回表达式的返回值

4. 如何判断某个文件是否存在

[[-e 1.md]] && echo '存在'

5. 如何直接在 shell 中计算 1+1=

echo $((1+1))

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/rfc6r8