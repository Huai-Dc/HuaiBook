## 浏览器缓存详解
### 浏览器缓存类型
- 强缓存：不会向服务器发送请求，直接从缓存资源中读取，在chrome控制台可以看到该请求返回200状态码，
    并且size显示 from disk cache 或 memory cache
- 协商缓存：向服务器发起请求，服务器会根据request header的一些参数判断是否命中协商缓存，
    若命中则返回304状态码并带上新的response header通知浏览器读取缓存
- 区别就是: 都是从客户端读取缓存资源，一个会发送请求，一个不会发送请求

### 缓存有关的header
#### 强缓存
- Expires：response header的过期时间，如果浏览器再次加载资源时，在过期时间内，则命中强缓存
- Cache-Control：当值设为max-age=300时，则表示在该请求正确返回的五分钟内，再次加载资源，就会命中强缓存
- Cache-Control（HTTP1.1）的优先级高于Expires（HTTP1.0）
- Expires 是一个具体服务器时间，如果客户端时间与服务器时间相差较大，结果会与开发者期望相悖，Cache-Control是一个时间段，控制就比较容易

#### 协商缓存
- etag：上一次加载资源时，服务器返回的response header，该资源的唯一标识，只要资源变化了etag 就会重新生成，
    浏览器下一次加载向服务器请求时，会将etag放到request header 里的 if-None-Match里服务器接收后会比较，若相同则命中协商缓存
- Last-Modified & If-Modified-Since，Last-Modified是该资源文件最后一次更改的时间，服务器会在response header里返回，
    浏览器在下一次请求时，放到request header里的If-Modified-Since里，服务器收到后会作对比，相同则命中缓存
- 区别：
    - 精度上 etag 优于 Last-Modified，Last-Modified单位是秒，若一秒内文件变化多次，并不会得到体现，如果是负载均衡的服务器，各个服务器生成的Last-Modified也可能不一致
    - 性能，etag要逊色于Last-Modified，etag 每次变化都需要重新计算etag值
    - 优先级，服务器校验优先考虑 etag
    
### 浏览器缓存过程
- 1.第一次加载时，服务器返回200，浏览器将资源从服务器下载下来，并缓存response header以及请求的返回时间
- 2.下一次请求时，先比较当前时间与缓存时间的时间差，若没有过期则命中缓存，不发送请求，读取本地资源，
    若过期则发送header带有If-None-Match和If-Modified-Since的请求；
- 3.服务器收到请求后，优先根据etag值判断有没有被修改，若一致则返回304命中缓存，若不一致则返回200，返回新的资源文件
- 4.若没有etag值，则将If-Modified-Since和被请求文件的最后修改时间做比对，一致返回304命中缓存，不一致返回新的last-Modified，返回200

### 用户行为对浏览器缓存的控制
- 地址栏访问，链接跳转是正常的用户行为，会触发浏览器缓存机制
- F5刷新，浏览器会设置max-age=0，跳过强缓存判断，进行协商缓存判断
- ctrl+F5，跳过协商缓存与强缓存，直接从服务器拉取资源

### 总结
- 强缓存通常针对静态资源
- 强缓存是前端性能优化的最有力工具，通常做法就是为静态资源配置一个很长的过期时间或Cache-Control
- 强缓存会带来发布时资源更新的问题，资源更新时，默认情况下浏览器不会请求服务器，除非清楚缓存或强制刷新。




