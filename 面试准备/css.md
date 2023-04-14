# css
## 盒模型

标准盒模型 默认值 box-sizing cotnent-box width = content  
怪异盒模型 box-sizing border-box width = content + margin + padding

## css 权重

!import 最高级 > style 内联样式 > id > class > 标签 伪类  
id 100  
class 伪类选择器 10  
标签 和 伪类 1

| **选择器**                | **权重**           |
| ------------------------- | ------------------ |
| ！important               | infinity（正无穷） |
| 行间样式（style）         | 1000               |
| id                        | 100                |
| class/属性/伪类（:hover） | 10                 |
| 标签（.xx)/伪元素         | 1                  |
| 通配符选择器（\*）        | 0                  |

## 水平垂直居中

flex  
grid place-items：center  
absolute/transform

flex: 1  
flex-auto 1  
flex-shrink 1  
flex-basis 0

## [如何画一个正方形/长宽固定的长方形](https://github.com/shfshanyue/Daily-Question/issues/547)

aspect-ratio 1/1

## css 如何避免样式冲突

scoped data-v-333418c3  
css-module 为每一个局部类赋予全局唯一的类名  
colorList1_list-box-data_qoynS

## css 变量

减少重复定义  
js 可以直接控制修改

## css 实现暗黑模式

@media (prefers-color-scheme: dark)  
prefers-color-scheme

```javascript
<style>
    html {
        -webkit-filter: grayscale(100%);
        -moz-filter: grayscale(100%);
        -ms-filter: grayscale(100%);
        -o-filter: grayscale(100%);
        filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
        _filter:none;
    }
</style>
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/llikozmsm2977rh4