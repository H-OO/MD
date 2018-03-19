## Ajax

**简单版**

```javascript
function Ajax(param) {
  const _param = param;
  // 默认配置
  let config = {
    url: '',
    type: 'GET',
    withCredentials: false,
    data: {},
    success: function() {}
  };

  for (let k in _param) {
    config[k] = _param[k];
  }

  const xhr = new XMLHttpRequest();

  xhr.open(config.type, config.url, true); // true表示异步

  xhr.onreadystatechange = function() {
    if (xhr.status === 200 && xhr.readyState === 4) {
      config.success(xhr.responseText);
    }
  };

  xhr.send(config.data);
}

Ajax({
  url: '/xxx',
  type: 'GET',
  withCredentials: false,
  data: {
    id: 'abc'
  },
  success: function(data) {
    console.log(data);
  }
});
```
