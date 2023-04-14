# day26 vim operator
### 粘贴复制

- yy 复制整行
- Y mac zsh 不生效
- p：mac zsh 光标下一行进行粘贴
- P：mac zsh 光标上一行进行粘贴

### delete

- dd：删除整行内容
- D：删除当前字符至行尾

### change

- cc：删除整行内容并进入 insert mode
- C：删除当前字符至行尾并进入 insert mode

### shift

- > > ：向右缩进
- <<：向左缩进

### operater + move

- dl 删除右侧字符（当前字符）
- dh 删除左侧字符

### text object

- daw：删除当前单词
- di(：删除括号内所有内容
- da(：删除括号内所有内容，包括括号
- ca(：删除括号内所有内容，包括括号，并进入 insert mode

### undo/redo/search

- u：撤销
- n：下一个搜索
- N：上一个搜索

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/mhdkvq