## session 工作原理
### 是什么？
- session是浏览器与服务器回话过程中，服务器分配的一块存储空间，服务器默认为浏览器在cookie中设置sessionId，浏览器在向服务端请求过程中传输cookie包含sessionId，，服务器根据sessionId 取出会话中的信息。
- 由于http协议是无状态的，即http请求一次连接一次，数据传输完毕，连接就会断开，下次访问需要重新连接
- 通过cookie中的sessionId和服务端的session关联，可以确定会话的身份信息

### session 比 cookie安全
- 用户信息可以通过加密存储到cookie，但是这样安全性较差，cookie容易被其他程序获取篡改。使用session的意义在于session存储在服务器，安全性较高

### session的生命周期
- 创建
    - 浏览器访问服务器的servlet（JSP）时，服务器会自动创建session，并把sessionId通过cookie返回到浏览器，servlet规范中，通过request.setSession(true),可以强制创建session
- 销毁
    - 服务器会默认给session一个过期时间，即从该session的会话在有效时间内没有被访问就会被设置为过期，需要重新建立会话
    
### session与cookie区别
- session 记录在服务器，cookie记录在浏览器
- session 保存的大小取决于服务器的程序设计，理论上可以不限，单个cookie大小不超过4kb，大多数限制一个站点20个cookie
- session 可以被服务器处理为 key-value的任何对象；cookie则是存在浏览器中的一段文本
- session 存储在服务器安全性高，cookie 存储在浏览器，容易被获取篡改，安全性低
- 大量的用户会话，服务器端保存大量session对服务器消耗较大；信息保存在cookie中缓解了服务器压力

- PS：正常情况下关键信息保存到session中，其他信息加密后保存在cookie中
