## T.对tree-shaking的了解
- 虽然生产环境下默认开启，但是由于经过babel编译全部模块被封装成IIFE
- IIFE存在副作用无法被tree-shaking调用
- 需要配置`{module: false}`和`sideEffects: false`
- rollup 和webpack的shaking程度不同以一个class为例子

### 介绍
- tree-shaking是一个术语，通常用于描述移除js上下文中的未引用代码

#### 添加一个模块
- 比如在项目中添加一个新的通用模块文件，此文件导出两个函数 A、B
- 在项目中导入了函数A，B在项目中未使用到
- 输出的bundle中会包含未使用的函数

#### 将文件标记为无副作用【sideEffectFree】
- 上面添加模块出现的问题会导致打包后的文件过大，所以我们会向webpack的compiler 提供提示哪些代码是无副作用的
- 可以通过配置 package.json的sideEffects 来提示
- 如果所有代码都不包含副作用，我们就可以简单的将属性标记为false 来告知webpack他可以安全的删除未使用的export
```json
{
    "name": "myProject",
    "sideEffects": false
}
```

```$xslt
副作用：在导入时会执行特殊行为的代码，而不是仅仅暴露一个export或多个export，
如 polyfill，会影响全局作用域，但是通常不提供export
```

- 若有些代码确实有副作用，可以提供一个数组

```json
{
  "name": "myProject",
  "sideEffects": [
    "./src/some-side-effectful-file.js"
  ]
}
```

```$xslt
任何导入的文件都会受到tree-shaking的影响，意味着如果在项目中使用类似css-loader并导入css文件，则需要将其添加到sideEffects 列表中，以免在生产模式中被删除
```

```json
{
  "name": "myProject",
  "sideEffects": [
    "./src/some-side-effectful-file.js",
    "*.css"
  ]
}
```

