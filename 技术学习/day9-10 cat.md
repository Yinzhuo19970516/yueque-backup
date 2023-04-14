# day9-10 cat
## cat 查看文件内容

无压力学习，本来就会  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658111767077-c4c897c7-5ef5-49c0-a0de-9e97a54d4246.png#clientId=u7a42b593-4063-4&from=paste&height=59&id=u741d1fd7&name=image.png&originHeight=118&originWidth=526&originalType=binary&ratio=1&rotation=0&showTitle=false&size=21152&status=done&style=none&taskId=ua2ccfdf9-2e9a-445b-87b4-4d26c94546c&title=&width=263)

## less 可以 vim 命令控制上下移动以及关键词搜索

## head tail

很语义化，无压力学习  
返回前十行 tail 返回后十行  
head -10 xxx  
tail -10 xxx

## pipe 管道

它将前边命令的标准输出（stdout）作为下一个命令的标准输入（stdin）。  
可以更精准的把控输出，也可以一次性输出自己想要的内容  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658112550579-afe43c51-6c10-456f-9e24-b8f94867bcbe.png#clientId=u7a42b593-4063-4&from=paste&height=59&id=ubbd79716&name=image.png&originHeight=118&originWidth=1086&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20353&status=done&style=none&taskId=u4cccff9c-b52d-4f22-97ee-02d61cb9968&title=&width=543)

## redirection

git 初始化仓库中，经常用，无压力学习  
echo hello > README.md  
echo hello >> README.md

## heredoc

便于我们连续输入多行文字  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658114188496-b04aaa55-d8c2-4b06-9eae-0a54d1070639.png#clientId=u3d23765f-131e-4&from=paste&height=201&id=u7389deca&name=image.png&originHeight=402&originWidth=650&originalType=binary&ratio=1&rotation=0&showTitle=false&size=45266&status=done&style=none&taskId=u3d102b4f-7266-49bc-bdc1-17251b09955&title=&width=325)

## 作业

1. cat 这个命令行的本义是什么

cat 命令三大作用 显示文件，组合文件，创建新文件  
最常用功能是读取文件

- 打开输入文件
- 读一段数据存在 buffer 里
- 将 buffer 里的数据写到文件输出里
- 重复上述过程直到文件的结尾

2. 什么是 heardoc

- HereDoc 全名叫做 Here Document，中文可以称之为 嵌入文档
- 分割串常见的为 EOF，但不一定固定为 EOF，可以使用开发者自行定义的
- 便于我们连续输入写入文件

## 作业

1. 如何查看换行符是 \n 还是 \r\n，可参考 [ASCII](https://devtool.tech/ascii/13)

\n 是换行  
\r\n 是回车换行  
 cat -e 代表显示不可打印字符与换行符。  
\n 会显示成$  
\r 会显示成 ^M

2. cat 这个命令行的本义是什么

cat 命令用来查看文件内容  
英文原意：concatenate files and print on the standard output。  
连接文件并打印输出到标准输出

3. 如何输出文件的前十行/后十行内容

head -10 xxx/tail -10 xxx

4. 如何实时输出日志的追加内容

tail -f XXX  
666 第一次学的时候咋没发现  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1663926495148-92c47ab9-04fe-4ef3-8935-6a0d70d3c9ed.png#clientId=u698edf65-2ac8-4&from=paste&height=359&id=u01917fd3&name=image.png&originHeight=718&originWidth=1774&originalType=binary&ratio=1&rotation=0&showTitle=false&size=233220&status=done&style=none&taskId=ue4ff8118-82eb-4677-b464-88ed5cf26e6&title=&width=887)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/mnwgli