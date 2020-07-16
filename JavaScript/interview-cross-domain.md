## 跨域

### 什么是跨域
- 前端调用的后端接口不属于同一个域(域名或端口不同),就会产生跨域

### 为什么会出现跨域
- 浏览器显示，而不是服务端限制，可以查看NetWork，请求能正确响应，response返回的值也是正确的
- 请求地址的域名或端口喝当前访问的域名或端口不一样
- 发送的XHR请求，可以使用a标签和img标签做对比
- 满足以上三个条件就会产生跨域

### 解决跨域问题的思路
- 客户端浏览器接触跨域限制（不现实）
- 发送JSONP请求替代XHR请求（不适用于所有请求方式仅支持GET，服务端需要修改代码，不推荐）
- 修改服务器端（推荐）
    - 服务器增加指定字段
    - HTTP服务器增加指定字段
    - Nginx反向代理

### ajax 跨域携带cookie

```js
$.ajax({
    type: "POST",
    url: "../url",
    data: JSON.stringify({name: 'tom'}),
    dataType: 'json',
    xhrFields: {
        withCredentials: true // 允许跨域携带cookie
    },
    success: function(){}
})
```

- 若还是出现跨域问题
    - 服务器需要配置Access-Control-Allow-Credentials

```js
response.setHeader("Access-Control-Allow-Credentials", "true")
```

    - 服务器配置Access-Control-Allow-Origin

```js
response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"))
```
