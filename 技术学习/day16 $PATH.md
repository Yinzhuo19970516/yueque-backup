# day16 $PATH
1.了解 $PATH 环境变量用途  
前面在使用 rm、rmdir、ls 等命令时，无论当前位于哪个目录，都可以直接使用，而无需指明命令的执行文件所在的位置（绝对路径）呢？其实，这是 PATH 环境变量在起作用。  
当执行某个命令时，Linux 会依照 PATH 中包含的目录依次搜寻该命令的可执行文件，一旦找到，即正常执行；反之，则提示无法找到该命令。  
PATH 变量就是用于保存可以搜索的目录路径，如果待运行的程序不在当前目录，操作系统便可以去依次搜索 PATH 变量变量中记录的目录，如果在这些目录中找到待运行的程序，操作系统便可以运行。  
2.which，列出全局命令的完整路径。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658719399547-53f4866c-a2d4-4cc1-b43d-3b6da9bc8eeb.png#averageHue=%230e0e0e&clientId=ue59c7651-11f6-4&from=paste&height=111&id=ub131d3f2&name=image.png&originHeight=222&originWidth=396&originalType=binary&ratio=1&rotation=0&showTitle=false&size=27692&status=done&style=none&taskId=u8f3a4c64-0f3d-4906-a1af-9b0e57fd83c&title=&width=198)  
3.如何设计一个可以切换 node 版本的命令行工具，比如 n 与 nvm  
先在全局一个文件夹存放所有 node 版本  
nvm use v14 会把 node14 版本的地址，通过软连接的方式切换到 nvm/的某个目录下  
后续当我们使用 node 的时候，会优先从 nvm 目录下查找 node 版本

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/qx54dg