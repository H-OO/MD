## JSONP跨域

原理：利用script标签的src属性的天然可跨域特性，后台将数据作为回调函数的参数进行传递  
使用时需要对请求地址进行参数序列化，加入与回调函数名相关的键值对  
注意JSONP跨域为GET请求

```javascript
// 跨域请求函数
function JSONP(param) {
  var url = param.url; // 请求路径（未序列化）
  var jsonp = param.jsonp; // 给后台获取回调名称的键名
  var success = param.success; // 成功后的回调
  // 参数序列化 + 设置成功回调函数名
  var random = Math.random().toString().replace('.', '_'); // 0_123123123123123
  var callbackName = 'jsonp_' + random; // jsonp_0_123123123123123
  url = url + '?' + jsonp + '=' + callbackName; // http://www.baseUrl.com?jsonpCallback=jsonp_0_123123123123123
  console.log(url);
  window[callbackName] = success; // 将成功回调函数全局化
  // 利用script标签的src属性进行跨域
  var scriptEle = document.createElement('script');
  scriptEle.src = url;
  document.body.appendChild(scriptEle); // 插入body最末
}
// 使用
JSONP({
  url: 'http://baseUrl.com', // 请求的基本路径
  jsonp: 'jsonpCallback', // 给后台获取回调名称的键名
  success: function (data) {
    console.log(data);
  }
});
```