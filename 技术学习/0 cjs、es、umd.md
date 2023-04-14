# 0 cjs、es、umd
以 vant 组件打包为例，build 之后，会生成的文件目录如下

```javascript
project
├─ es                  # es 目录下的代码遵循 esmodule 规范
│   ├─ button          # button 组件编译后的代码目录
│   └─ index.js        # 引入所有组件的入口 (ESModule)
│
└─ lib                  # lib 目录下的代码遵循 commonjs 规范
    ├─ button           # button 组件编译后的代码目录
    ├─ index.js         # 引入所有组件的入口
    ├─ [name].js        # 打包后的组件脚本，UMD 格式
    ├─ [name].es.js     # 打包后的组件脚本，ESModule 格式
    ├─ [name].cjs.js    # 打包后的组件脚本，cjs 格式
```

cjs 和 umd 已经很熟悉了  
umd，其实在学习模块化内容的时候，我只看 require 和 import，其余的默认忽略了  
我在开发中用得比较少  
个人感觉写法太过复杂了，太复杂的东西要么变简单，要么被淘汰，我觉得它离淘汰不远了。

```javascript
(function (root, factory) {
  if (typeof define === "function" && define.amd) {
    // AMD
    define(["jquery"], factory);
  } else if (typeof exports === "object") {
    // Node, CommonJS之类的
    module.exports = factory(require("jquery"));
  } else {
    // 浏览器全局变量(root 即 window)
    root.returnExports = factory(root.jQuery);
  }
})(this, function ($) {
  //    方法
  function myFunc() {}

  //    暴露公共方法
  return myFunc;
});
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/fpgpgf