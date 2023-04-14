# vue webpack 更换主题N种方案优劣分析
### vue+webpack 更换主题 N 种方案优劣分析

由于最近需要用同一套代码对接多家合作方，每家合作方主题要求独立，这就要求我们项目支持全局皮肤切换  
调研了下当前行业的实现方案，五花八门良莠不齐，在此总结下各种方案有优劣及复杂度，供大家快速定位到符合自己业务的方案，以 vue 单页应用为业务场景

### 方式一：class 切换方式

##### 实现：

在每个需要更换的页面定义多个 class，根据运行时标识动态的切换 class 名称实现加载不同的样式，这种方式较为简单

##### 优点：

- 不需要修改构建工具相关
- 业务开发过程可以实现，没有限制
- 支持动态切换

##### 缺点：

- 逻辑分散耦合在各个页面，一旦需要修改，涉及修改的页面较多
- 代码需要预先内置，不支持动态颜色修改

### 方式二：ElementUI 的实现

##### 实现：

- 对主题涉及的颜色使用特殊值
  - 如:UI 需要白色#ffffff 色值的时候使用一个相近的特征值颜色 如:#fffffe

```
 // 将默认样式特征值替换为变量，用于多次替换
getStyleTemplate(data) {
  const colorMap = {
    '#ff0001': 'text_color',
    '#008001': 'bg_color'
  };
  Object.keys(colorMap).forEach(key => {
    const value = colorMap[key];
    data = data.replace(new RegExp(key, 'ig'), value);
  });
  return data;
},
```

- 在代码运行时动态获取到需要修改的颜色值
  - 如: 需要修改#fffffe =》 #ff00ff
  -

```
// 通过用户操作或者接口，获取到要替换的色值
colors:{
  text_color: '#f44336',
  bg_color: '#009688'
}
```

- 查找页面所有 style 标签将其色值#fffffe 正则匹配出来，替换为 #ff00ff

```
// 获取默认样式,可以从已加载的 style 中获取也可以从 css link 获取
getIndexStyle() {
  document.querySelectorAll('style').forEach(item=>{
    this.originalStyle += this.getStyleTemplate(item.textContent);
  })
},
getCssLink(){
  this.get('./cssPath.css').then(json=>{
    this.originalStyle = this.getStyleTemplate(json.data);
  })
}
```

- 在页面新加标签 style 覆盖默认值

```
// 替换默认样式表，插入 style 标签覆盖样式
writeNewStyle() {
  let cssText = this.originalStyle;
  log(cssText)
  Object.keys(this.colors).forEach(key => {
    cssText = cssText.replace(new RegExp('(:|\\s+)' + key, 'g'), '$1' + this.colors[key]);
  });
  cssText = cssText.replace(/\n/g,'')
  if (this.originalStylesheetCount === document.styleSheets.length) {
      const style = document.createElement('style');
      style.innerText = cssText;
      document.head.appendChild(style);
   } else {
      document.head.lastChild.innerText = cssText;
   }
}
```

##### 优点：

- 支持动态切换
- 支持动态色值
- 不需要内置多份样式

##### 缺点：

- 业务开发过程中需要对 ui 给出的色值重定义，业务开发需要有一定的规则
- **无法对懒加载的样式做处理，需要初始化加载全局所有样式**

const ExtractTextPlugin = require('extract-text-webpack-plugin') // 抽离 css module: { loaders: [ {//抽离 css 通过 link 加载 test: /\.css$/, loader: ExtractTextPlugin.extract({ fallback: 'style-loader', use: 'css-loader' }) } ... plugins: [ new ExtractTextPlugin({ filename: 'css/[name].css' allChunks: true // 打包所有页面 css 到同一个 css 文件 }) ]

- 无法动态修改背景图片

### 方式三：编译时多主题全量构建

##### 实现：

- 定义多套样式
- 构建时将多套样式主题作为独立构建入口，构建出主题静态文件 css 文件
- 业务运行时动态加载不同的主题文件

优点：

- 支持动态切换主题
- 业务开发样式分离
- 编译时构建性能较好  
  缺点：
- 构建工具配置较为复杂，适用单入口应用，对多入口的支持不友好
- 不支持 scoped
- 业务需要额外操作

配合 rel="alternate stylesheet" 可预加载备选主题样式

### 方式四： 编译时选择性构建

##### 实现：

- 内置多套皮肤
- 构建时传入参数，根据不同的构建参数加载不同的主题样式文件

##### 优点：

- 构建工具配置较为简单，不需要业务做额外操作
- 多入口应用支持度好

##### 缺点：

- 不支持动态切换
- 多个项目需要构建多次，需要构建系统支持

### 方式五：less 动态变量

##### 实现：

- 修改构建脚本，将所有页面 less 文件抽到同一个文件中
- 不编译 less，页面直接加载 less 文件
- 使用 less.js 在客户端编译 less 文件

less: { modifyVars: {}, javascriptEnabled: true }

##### 优点：

- 支持动态切换
- 支持动态色值

##### 缺点：

- 客户端编译较耗性能/耗时
- 需要额外加载 less.js mini 文件 size: 131KB

### 方式六：css 变量

##### 实现：

- 在需要变化的 css 属性定义变量

:root { --main-bg-color: pink; } body { background-color: var(--main-bg-color); }

- 在运行时动态的修改变量

document.body.style.setProperty('--primary', '#7F583F');

##### 优点：

- 浏览器原生支持，无需额外操作
- 支持动态色值

##### 缺点：

- 低版本兼容性不好  
  ios Safari 9.3、 android 5、 chrome forAndroid 76

UC、QQ、Baidu 等国内浏览器支持度较差

### 方式七： import 动态加载

##### 实现：

- 业务中预定义多套主题
- 运行时根据变量动态加载对应主题

if(a){ import('./thems/a/base.less') }else if(b){ import('./thems/b/base.less') }

##### 优点：

- 支持动态切换
- 实现简单

##### 缺点：

- 不支持动态色值
- 不支持 scoped
  | **方式** | **动态切换** | **动态色值** | **scoped** | **实现复杂度** | **兼容性** | **性能** | **维护性** |
  | --- | --- | --- | --- | --- | --- | --- | --- |
  | class 切换方式 | 是 | 否 | 是 | 简单 | 良好 | 良好 | 差 |
  | ElementUI 的实现 | 是 | 是 | 是 | 中等 | 良好 | 差 | 一般 |
  | 编译时多主题全量构建 | 是 | 否 | 否 | 复杂 | 良好 | 一般 | 优 |
  | 编译时选择性构建 | 否 | 否 | 否 | 中等 | 良好 | 良好 | 优 |
  | less 变量 | 是 | 是 | 否 | 复杂 | 良好 | 差 | 优 |
  | css 变量 | 是 | 是 | 是 | 中等 | 差 | 良好 | 优 |
  | import 动态加载 | 是 | 否 | 否 | 简单 | 良好 | 良好 | 优 |

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/cw2f16