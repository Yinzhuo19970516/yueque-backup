# day20 数组与字典
## 注意点

zsh 数组下标从 1 开始  
= 左右两边不能有空格

## 作业

1. 如何定义数组与字典

list=[1,2,3,4]  
typeset -A object  
declare -A object

2. 如何打印数组的全部值

ehco $list[@] 打印数组  
echo ${(k)obj[@]}

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/byx0iw