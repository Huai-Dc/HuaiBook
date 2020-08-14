## Vue 鉴权
### 需求
- 前端路由鉴权，屏蔽地址栏入侵
- 路由数据由后台管理，前端只按照固定规则异步加载路由
- 权限控制颗粒化精确到每一个按钮
- 自动更新token
- 同一个浏览器只能登陆一个账号


### 前端方案
```$xslt
对于需求1-2-3 采取异步加载路由方案
```
- 编写vue全局路由守卫
- 排除登陆路由和无需鉴权路由
- 登陆后请求拉取用户菜单数据
- 在vuex里处理菜单和路由匹配数据
- 在vuex里处理好的路由数据通过addRoutes异步加载到路由中

```js
router.beforeEach((to, from, next) => {
    const hsaToken = getToken()
    if(hasToken){
        // 判断当前用户是否已经拉取权限菜单
        if(store.state.sidebar.userRouter.length === 0){
            // 无菜单时拉取
            getMenuRouter().then(res => {
                let _menu = res.data.Data.ColumnDataList || [];
                store.commit("SET_MENU_ROUTER", _menu);
                router.addRoutes(store.state.sidebar.userRouter);
                next({...to, replace: true})
            }).catch(err => {})
        }else{
            // 当有用户权限时，说明所有可访问路由已经生成，如访问没权限的菜单会自动进入404
            if(to.path === "/login"){
                next({
                    name: "index"
                })
            }else{
                next()
            }
        }
    }else{
        // 未登录
        if(whiteList.indexOf(to.path) !== -1){
            next()
        }else{
            next(`/login?redirect=${to.path}`)
        }
    }
    
})
```

### 常用方式
- 渲染菜单时控制模块按钮的显示隐藏， 但是直接输入路由任然可以访问
- 在路由导航守卫处拦截，针对无权限路由进行重定向，（不足，每次访问模块都需要鉴权，模块数量太多会影响系统性能）
- 借助vue2中的API addRouters动态添加路由信息，（不足，首次加载需要解析与添加，多跳转一次路由）（推荐）
```$xslt
PS：选择最后一种方式，首屏加载会多0.5s，加载一次后就不需要处理
```


