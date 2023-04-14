# uni-app热更新方案
热更方案：  
背景：uni-app 如果独立发版本，则需要热更，下载最新版本是异步过程  
现行热更方案：  
发布平台选择，强制更新，当用户进入 uni-app,检测更新，下载，然后立即重新载入小程序，用户有明显的刷新替换包的过程，会出现用户操作一半，刷新进入计算器页  
发布平台选择，非强制更新，当用户进入 uni-app,检测更新，下载包。不阻塞用户操作，当用下一次重新进入 uni-app，用新包替换老包，打开后就是新的版本，无刷新过程。  
一般重大 bug,阻塞流程选择强制更新，其他选择静默更新  
如果 app 版本发布，会内置最新的 uni-app 包, 不会出现热更问题  
优化方向：  
强制更新对用户更加友好，处理方式更加优雅  
优化方案：  
uni-app 需要强制更新时，在打开 uni-app 时，进行明确的弹窗提示，如果用户选择确认，则进行强制热更新，如果选择否，走非强制更新  
打开 app 时，都进行 uni-app 版本判断，如果有最新版本，就去默认下载最新包（500k 左右）。如果下载完毕，下次打开 uni-app 就是最新版本，如果下载没有完毕进入 uni-app 则走老版本。  
打开 app 时，都进行 uni-app 版本判断，如果有最新版本，弹窗提示用户下载。选择下载，或则忽略  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1655303907934-898fe03a-7959-44ef-93a0-2a254644c36d.png#clientId=ub76eb3d9-d040-4&from=paste&id=ufa6a5f0e&name=image.png&originHeight=749&originWidth=754&originalType=url&ratio=1&rotation=0&showTitle=false&size=58010&status=done&style=none&taskId=udbe69b00-9a04-4bc7-a9bc-56ee3d48b9a&title=)  
微信小程序热更新方案  
微信客户端会有若干个时机去检查本地缓存的小程序有没有新版本，并进行小程序的代码包更新  
未启动时更新  
启动时更新

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/muhlmt