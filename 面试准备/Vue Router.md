# Vue Router
## 概述

在 vue-router 单页面应用中，则是路径之间的切换，也就是组件的切换。**路由模块的本质 就是建立起 url 和页面之间的映射关系**。

## Vue Router 实现原理

SPA 的核心之一就是更新视图而不重新请求页面  
路由器对象实现三大步骤

- 监视地址栏的变化
- 查找当前路径对应的页面组件
- 讲找到的页面组件替换到 router-view 的位置

vue-router 在实现单页面前端路由时，提供了两种方式：Hash 模式和 History 模式；vue2 是根据 mode 参数来决定采用哪一种方式，vue3 则是 history 参数

## hash

- hash（#）是 URL 的锚点，代表的是网页中的一个位置
- hash 虽然出现在 URL 中，但不会被包括在 HTTP 请求中。它是用来指导浏览器动作的，对服务器端完全无用，因此，改变 hash 不会重新加载页面
- window.addEventListener("haschange",event,false)

## History

- 利用了 HTML5 History Interface 中新增的 pushState() 和 replaceState() 方法。
- 这两个方法应用于浏览器的历史记录栈，在当前已有的 back、forward、go 的基础之上，它们提供了对历史记录进行修改的功能。只是当它们执行修改时，虽然改变了当前的 URL，但浏览器不会立即向后端发送请求。
- 开发环境- historyApiFallback

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/iiau97