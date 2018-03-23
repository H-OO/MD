## URL Scheme

通过 URL Scheme可以在H5页面唤醒手机中的APP（iOS和Android都支持）

**URL Scheme是什么**

由于苹果的app都是在沙盒中，相互是不能访问数据的。但是苹果还是给出了一个可以在app之间跳转的方法：URL Scheme  
简单的说，URL Scheme就是一个可以让app相互之间可以跳转的协议。每个app的URL Scheme都是不一样的，如果存在一样的URL Scheme，那么系统就会响应先安装那个app的URL Scheme，因为后安装的app的URL Scheme被覆盖掉了，是不能被调用的。  
而且，安卓也支持URL Scheme。

**URL Scheme的使用**

```javascript
var urlScheme = 'app://test'; // 与app端约定的
window.location.href = urlScheme; // 抛出scheme
// 成功会弹出提示框给用户选择，是否唤醒APP
// 失败则没有任何反应
```
