## 传统的vue纯浏览器渲染
- 不利于seo  爬虫不抓取js抓取html
- 首屏过慢
- 浏览器负担过重
- 内存占用大

## SSR
- 将js等在node上跑将node端执行的html给到浏览器

- 组成
    - 服务端打包入口 =》 生成服务端代码
    - 客户端打包入口 =》 生成客户端代码
    
- 特性
    - 每次都会创建一个vue实例
    - ssr 核心库 vue-server-render
    - 只会触发 beforeCreated与created钩子
    
    
