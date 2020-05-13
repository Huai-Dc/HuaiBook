## 打包优化
### 目的
- 减小包的大小，加快首屏载入速度

### 解决方案
#### 组件按需加载  babel-plugin-component 

#### DLL 打包优化
- 将静态资源文件（运行依赖包）与源文件分开打包，先使用DllPlugin给静态资源打包，再使用DLLReferencePlugin 让源文件引用资源文件
- 当我们引用了多个较大的包后，这些包本身不会运行，我们也不会去修改，但是当我们修改业务代码后 这部分包会被重新打包，极大浪费时间，使用这个工具预先处理就能有效的避免该问题
- step 1 安装以下包
```js
yarn add webpack-cli add-asset-html-webpack-plugin clean-webpack-plugin -D
```
- step 2 根目录下创建 webpack.dll.config.js 文件
```js
// webpack.dll.config.js
const path = require('path');
const webpack = require('webpack');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

const dllPath = 'public/vendor'

module.exports = {
    entry: {
        vendors: ['vue', 'vuex', 'axios', 'vue-router', 'view-design']
    },
    output: {
        path: path.join(__dirname, dllPath), // 打包后输出位置
        filename: '[name].dll.js',
        library: '[name]_library'
        // 此处是给DllPlugin 的name
        // 需要和底下 plugin name 保持一致
    },
    plugins: [
        // 清除之前的dll 文件
        new CleanWebpackPlugin(),
        // 设置环境变量
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: JSON.stringify('production')
            }
        }),
        // manifest.json 描述动态链接库包含了哪些内容
        new webpack.DllPlugin({
            path: path.join(__dirname, dllPath, '[name]-manifest.json'), // manifest 生成的文件夹及名字，
            name: '[name]_library',     // 与 output library 保持一致即可
            context: __dirname
        }),
    ]
};
```
- 此处的DLLPlugin 会生成一个 manifest.json 的文件，该文件供webpack.config.js 中加入 DLLReferencePlugin 使用
- step 3 预打包资源文件
```js
    // 可写入 package.json script 中
    "dll": "webpack -p --progress --config ./webpack.dll.config.js",
```
- step 4 通过webpack addAssetsHtmlPlugin 将打包的文件 注入 html


#### 按需加载
- 路由的按需加载（懒加载）
    - resolve => require(['@/components/Test'], resolve)
    ```js
    routes: [
          {
              path: './demo',
              component: resolve => require(['@/components/Test'], resolve)
          }
    ]
    ```
    - () => import('@/components/Test')
- 组件按需加载（异步组件）
    - webpack 内置语句 import(*)
    ```jss
      changeTab(tab){
          import('./_${tab}.vue').then(item => {
              Vue.component(tab, item.default)
          })
      }
    ```
    - 按需加载组件
- cdn加速
- 代码压缩
