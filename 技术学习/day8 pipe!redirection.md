# day8 pipe/redirection
1. > 与 >> 的区别是什么

- > ：将文件描述符或标准输出中内容写入文件

是清空并添加新内容

- > > ：将文件描述符或标准输出中内容追加入文件  
  > > 是在文件内容后面追加新内容

2. stdin/stdout 的文件描述符各是多少

- stdin 0
- stdout 1

3. 什么是 Here Document

   <<EOF，称作 Here Document，当最终写入 EOF（End of line）时，则 heardoc 会停止输入

4. 如何不显示某个命令的日志

表示把标准输出到空文件设备，也就是将打印信息丢弃掉，屏幕上什么也不显示。  
 echo hello > /dev/null

- /dev/null ：表示空设备文件

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/igghvr