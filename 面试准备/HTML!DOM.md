# HTML/DOM
## 统计当前页面出现次数做多的标签

document.querySeclectorAll('_')  
document.getElementsByTagName("_");  
遍历所有标签类数组，取出名字，存储尽数组 tagName，  
遍历 tagName,存储进 tagObj,key 为 tag,value 为出现次数  
遍历 tagObj，找到出现最多的次数

## 跨域

端口 协议 域名  
cors 在服务器端设置几个响应头，如 Access-Control-Allow-Origin: \*  
反向代理 nginx/dev-server  
jsonp

- 创建一个 script 标签
- script.src 加载的脚本内容为 JSONP
- 一般处理 get 请求

## 图片的懒加载

相交检测通常要用到事件监听，并且需要频繁调用 [Element.getBoundingClientRect()](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect) 方法以获取相关元素的边界信息。事件监听和调用 [Element.getBoundingClientRect()](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect) 都是在主线程上运行，因此频繁触发、调用可能会造成性能问题。这种检测方法极其怪异且不优雅。  
Intersection Observer  
img.src = img.dataset.src  
unobserver

## sessionStroage 和 localStorage

相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享 sessionStorage 的信息。

## 如何设置一个支持过期时间的 localStorage

{ **value, **expires }  
设置值时，增加一个过期时间，超时删除数据

## Cookie 属性

- Domain
- path
- Expire/MaxAge
  - 不设置 max-age 和 expires，此 cookie 就是会话级别的，浏览器关闭就没了
- HttpOnly
  - 是否允许被 JavaScript 操作
- Secure
- SameSite
  - None 任何情况下都会向第三方网站请求发送 Cookie
  - Lax 任何情况下都不会向第三方网站请求发送 Cookie，get 请求，post 请求。 默认值
  - Strict 任何情况下都不会向第三方网站请求发送 Cookie

## 删除设置 cookies

document.cookies = 'a=3;max-age=01'  
cookieStore.delete(name)

## e.currentTarget 与 e.target 有何区别

e.target 是触发元素  
e.currentTarget 是绑定事件的父元素

## input 事件

Vue 的 input 有多个监听事件，包括：

- v-model：用于双向绑定数据，当 input 的 value 值发生变化时，会自动更新绑定的数据；
- @input：用于监听 input 的输入事件，每当 input 的 value 值发生变化时，都会触发该事件；
- @change：用于监听 input 的值改变事件，当 input 的 value 值被改变并且失去焦点时，会触发该事件；
- @focus：用于监听 input 的聚焦事件，当 input 被聚焦时，会触发该事件；
- @blur：用于监听 input 的失焦事件，当 input 失去焦点时，会触发该事件。 这些事件可以通过在 input 标签上添加相应的属性或者使用@符号绑定事件来监听。

## 如何取消请求

以下两种 API 的方式如下

- XHR 使用 xhr.abort()
- fetch 使用 AbortController

## 如何把 DOM 转化为图片

htm2canvas  
DOM -> SVG -> Canvas -> JPEG/PNG

## 异步加载 JS 脚本时，async 与 defer 有何区别

js 脚本 加载 解析 执行 都会阻塞 dom 渲染，因此一般放后头  
async 加载完后立即执行，async 适用于不需要等待页面解析完毕的脚本，比如 Web 分析工具、广告等  
defer 加载完后，到 dom 解析完才会执行，但是在 domContentLoaded 之前

使用场景方面，async 适用于不需要等待页面解析完毕的脚本，比如 Web 分析工具、广告等；而 defer 适用于需要等待页面解析完毕之后再执行的脚本，比如用于操作 DOM 元素的脚本。不过需要注意的是，在一些旧版浏览器中，async 和 defer 并不被支持，因此在使用时需要考虑兼容性问题。

## React/Vue 中的 router 实现原理如何

history API

- 通过 history.pushState() 跳转路由
- 通过 popstate event 监听路由变化，但无法监听到 history.pushState() 时的路由变化

当代码调用 history.back() 或 history.forward() 方法时，都会触发 popstate 事件。但是，如果你使用 history.pushState() 方法来改变路由，这种方法并不会触发 popstate 事件，而是需要手动触发事件才能监听到路由的变化。  
hash

- 通过 location.hash 跳转路由
- 通过 hashchange event 监听路由变化

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/owmgcxn4fw3cyc9i