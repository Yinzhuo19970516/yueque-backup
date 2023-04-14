# 请你谈谈 rem 和 px 的区别
## rem

rem em 是相对单位，根节点，父节点增加了 font-size，根据字体倍数来定义尺寸，距离

## rem 如何适配各种宽度屏幕

flexible.js 1rem=1/10 个视图宽度

```javascript
var docEl = document.documentElement; // 根元素，即<html></html>
function setRemUnit() {
  var rem = docEl.clientWidth / 10; // 设置根元素的font-size，也就是1rem的大小（设置为1/10视图宽度）
  docEl.style.fontSize = rem + "px";
}
setRemUnit();
window.addEventListener("resize", setRemUnit);
```

## rpx

rpx 是相对单位 1rpx = 1/750vw

## px

逻辑像素 Points、渲染像素 Rendered Pixels、物理像素 Physical Pixels、设备 Physical Device  
<metaname="viewport"content="width=device-width,initial-scale=1">  
添加这段代码后，那么 px 就对应 Points，它基本跟真实机型尺寸是成比例的，不会受 ppi(pixel per inch)每英寸像素的影响。此时在各种设备下，人肉眼看 16px 的字都是正常字号

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/zrnkma