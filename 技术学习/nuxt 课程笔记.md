# nuxt 课程笔记
地址：[https://juejin.cn/video/7202149403342143520/section/7202885295820242947?enter_from=course_center&utm_source=course_center](https://juejin.cn/video/7202149403342143520/section/7202885295820242947?enter_from=course_center&utm_source=course_center)

## 简介

nuxt 是一个基于 vue 的上层全栈通用框架

## 优势

基于文件路由支持，内置 vue-router  
依赖库，组件库，工具集自动导入  
零配置 ts 支持  
插件，模块，中间件，层复用机制扩展能力  
也有一定的服务端支持能力  
支持多种渲染机制

## 五种渲染模式

- 客户端渲染 SPA；
  - 需等待浏览器下载执行 js 的时间
  - 由于页面内容一开始为空，也会导致搜索引擎无法爬取结果，不利于 SEO
- 服务端渲染 SSR；
  - 服务端渲染好 html 返回给前端，提供了快速加载首屏的能力，这同时也解决了 SEO 问题
  - 组件使用有差异，增加了服务负载，适合博客，官网
  - nuxt3 默认渲染模式是 SSR
- 全静态内容生成 SSG；
- 混合渲染模式 Hybrid；
- 边缘渲染 Edge-render。
  - 提供了快速加载首屏的能力，这同时也解决了 SEO 问题。

## 路由根据文件名自动生成

- 基本路由
- 动态路由
- 潜逃路由

## 布局系统

Nuxt 提供了布局系统，可以把公用的页面布局内容提取到 layouts 目录中以便复用。

## 自动导入

Nuxt3 中会处理以下依赖的自动导入。

- Nuxt 自动导入：数据访问 useFetch、状态管理 useState、App 上下文 useNuxtApp、运行时配置 useRuntimeConfig 等等。
- Vue 自动导入：ref、reactive、computed 等等。
- 基于路径自动导入：
  - 组件目录：/components ；
  - hooks 目录：/composables ；
  - 工具库目录：/utils

## 访问 API

- $fetch；
- useAsyncData；
- useLazyAsyncData；
- useFetch；
  - useFetch 是对 useAsyncData 和$fetch 的封装，只需传入请求的 URL 或者一个请求函数即可，一些选项会自动填入，用起来最简洁，是最推荐的数据获取方式。
- useLazyFetch
  - 该方法等效于 useFetch 设置了 lazy 选项为 true，不同之处在于它**不会阻塞路由导航**，这意味着我们需要处理 data 为 null 的情况

## Nuxt 生命周期分类

- Nuxt 钩子；
- Vue App 钩子；
- Nitro App 钩子。

## 中间件

- Route middleware：路由中间件；
  - 路由守卫
- Server middleware：服务器中间件。
  - 接口拦截保护

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/gbgpn4tzuco3ywa7