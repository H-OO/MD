## 修改php配置文件
* 后台框架 => cmf-php  
* 本地服务 => Apache  
* Apache运行目录指向存放index.php启动文件的文件夹  
* 通过修改本地host文件，设置域名对应的IP地址  

-----------------------------------------------------------------

### Access-Control-Allow-Credentials 请求是否携带SESSID
```javascript
  // jquery ajax
  $.ajax({
    xhrFields: {withCredentials: true}
  })
  // vue axios
  axios.defaults.withCredentials = true
```
-----------------------------------------------------------------

### Access-Control-Allow-Origin 是否允许跨域
php文件修改：  
将 `Access-Control-Allow-Origin`设置为`*` 允许所有请求跨域  
这样做能解决跨域问题，但是这样无法使用SESSIDID，后端不能判断用户是否已经登录  
正确的操作：  
将`Access-Control-Allow-Origin`设置成与本地一致，例如`http://localhost:80`  

-----------------------------------------------------------------

### SESSID与token的区别
SESSID在发送任何请求就会生成一个，保存在cookie中；登录后SESSID会在后台中指向所登录用户的所有信息  
token只有登录后才能获得，保存在浏览器端，每次请求都要将其作为参数携带过去，用于后端的登录状态校验  

用途：  
SESSID可用于获取图片验证码进行登录的操作  
token多用于H5项目  

-----------------------------------------------------------------

### Access-Control-Allow-Headers 设置请求头
将`Access-Control-Allow-Headers`设置为`X-Requested-With,Content-Type,XX-Device-Type,XX-Token`

-----------------------------------------------------------------

### Access-Control-Allow-Methods 设置请求的方式
将`Access-Control-Allow-Methods`设置为`GET,POST,PATCH,PUT,DELETE,OPTIONS`
