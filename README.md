## 一些好的UI库


## 一、Ant Design Vue
[跳转到官网](https://www.antdv.com/docs/vue/introduce-cn/)
#### 特性
###### 提炼自企业级中后台产品的交互语言和视觉风格。
###### 开箱即用的高质量 Vue 组件。
###### 共享Ant Design of React设计工具体系。

#### 支持环境
###### 1.现代浏览器和 IE9 及以上（***需要 polyfills***）。
###### 2.支持服务端渲染。
###### 3.Electron


#### 安装
>##### 推荐使用 npm 或 yarn 的方式进行开发，不仅可在开发环境轻松调试，也可放心地在生产环境打包部署使用，享受整个生态圈和工具链带来的诸多好处。

>

```node
$ vue create antd-demo

$ npm i --save ant-design-vue

$ npm run serve



# npm install ant-design-vue --save


```


>修改 src/main.js，引入 antd 的按钮组件以及全部样式文件
```js
import Vue from 'vue';
import Button from 'ant-design-vue/lib/button';
import 'ant-design-vue/dist/antd.css';
import App from './App';

Vue.component(Button.name, Button);

Vue.config.productionTip = false;

new Vue({
  render: h => h(App),
}).$mount('#app');

```

>修改 src/App.vue的 template 内容
```html
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <a-button type="primary">Button></a-button>
  </div>
</template>
...
```

>现在你应该能看到页面上已经有了 antd 的蓝色按钮组件，接下来就可以继续选用其他组件开发应用了。


***现在已经把组件成功运行起来了，但是在实际开发过程中还有很多问题，例如上面的例子实际上加载了全部的 antd 组件的样式（对前端性能是个隐患）。***
>此时我们需要对 vue-cli 的默认配置进行自定义

### 使用 babel-plugin-import
babel-plugin-import 是一个用于按需加载组件代码和样式的 babel 插件
```js
yarn add babel-plugin-import --dev
```

>使用 vue-cli 2 

修改.babelrc文件，配置 babel-plugin-import

```js
  {
    "presets": [
      ["env", {
        "modules": false,
        "targets": {
          "browsers": ["> 1%", "last 2 versions", "not ie <= 8"]
        }
      }],
      "stage-2"
    ],
-   "plugins": ["transform-vue-jsx", "transform-runtime"]
+   "plugins": [
+     "transform-vue-jsx",
+     "transform-runtime",
+     ["import", { "libraryName": "ant-design-vue", "libraryDirectory": "es", "style": "css" }]
+   ]
  }

```

>使用 vue-cli 3

修改babel.config.js文件，配置 babel-plugin-import
```js
 module.exports = {
  presets: ["@vue/app"],
+  plugins: [
+    [
+      "import",
+      { libraryName: "ant-design-vue", libraryDirectory: "es", style: true }
+    ]
+  ]
};

```

>然后移除前面在 src/main.js 里全量添加的 import 'ant-design-vue/dist/antd.css'; 样式代码，并且按下面的格式引入模块。

```js
  // src/main.js
  import Vue from 'vue'
- import Button from 'ant-design-vue/lib/button';
+ import { Button } from 'ant-design-vue';
- import 'ant-design-vue/dist/antd.css'
  import App from './App'

  Vue.component(Button.name, Button)

  Vue.config.productionTip = false

  new Vue({
    render: h => h(App)
  }).$mount("#app");
  
```
 ***最后重启 npm run serve 访问页面，antd 组件的 js 和 css 代码都会按需加载，你在控制台也不会看到这样的警告信息。***
 
 
 
 
 
 
## 定制主题
### 一、Ant Design Vue 的样式变量
>antd 的样式使用了 Less 作为开发语言，并定义了一系列全局/组件的样式变量。

>以下是一些最常用的通用变量，所有样式变量可以在 这里 找到。
```css
@primary-color: #1890ff; // 全局主色
@link-color: #1890ff; // 链接色
@success-color: #52c41a; // 成功色
@warning-color: #faad14; // 警告色
@error-color: #f5222d; // 错误色
@font-size-base: 14px; // 主字号
@heading-color: rgba(0, 0, 0, 0.85); // 标题色
@text-color: rgba(0, 0, 0, 0.65); // 主文本色
@text-color-secondary: rgba(0, 0, 0, 0.45); // 次文本色
@disabled-color: rgba(0, 0, 0, 0.25); // 失效色
@border-radius-base: 4px; // 组件/浮层圆角
@border-color-base: #d9d9d9; // 边框色
@box-shadow-base: 0 2px 8px rgba(0, 0, 0, 0.15); // 浮层阴影

```

#### 定制方式
>在 webpack 中定制主题

以 webpack@4 为例进行说明，以下是一个 webpack.config.js 的典型例子，对 less-loader 的 options 属性进行相应配置。
```js
// webpack.config.js
module.exports = {
  rules: [{
    test: /\.less$/,
    use: [{
      loader: 'style-loader',
    }, {
      loader: 'css-loader', // translates CSS into CommonJS
    }, {
      loader: 'less-loader', // compiles Less to CSS
+     options: {
+       lessOptions: {
+         modifyVars: {
+           'primary-color': '#1DA57A',
+           'link-color': '#1DA57A',
+           'border-radius-base': '2px',
+         },
+         javascriptEnabled: true,
+       }
+     },
    }],
    // ...other rules
  }],
  // ...other config
}
```
***注意 less-loader 的处理范围不要过滤掉 node_modules 下的 antd 包。***

>在 vue cli 2 中定制主题

修改build/utils.js文件

```js
// build/utils.js
- less: generateLoaders('less'),
+ less: generateLoaders('less', {
+   modifyVars: {
+     'primary-color': '#1DA57A',
+     'link-color': '#1DA57A',
+     'border-radius-base': '2px',
+   },
+   javascriptEnabled: true,
+ }),
```

>在 vue cli 3 中定制主题

项目根目录下新建文件vue.config.js
```js
// vue.config.js for less-loader@6.0.0
module.exports = {
  css: {
    loaderOptions: {
      less: {
        lessOptions: {
          modifyVars: {
            'primary-color': '#1DA57A',
            'link-color': '#1DA57A',
            'border-radius-base': '2px',
          },
          javascriptEnabled: true,
        },
      },
    },
  },
};

```

#### 使用这个框架需要安装
>npm i --save ant-design-vue

***错误提示***

npm WARN ant-design-vue@1.7.0 requires a peer of vue-template-compiler@>=2.6.0 but none is installed. You must install peer dependencies yourself.
>npm WARN -design-vue@1.7.0需要vue-template-compiler@>=2.6.0，但是没有安装。您必须自己安装对等依赖项。


npm WARN @ant-design/icons-vue@2.0.0 requires a peer of vue-template-compiler@>=2.5.0 but none is installed. You must install peer dependencies yourself.

>npm WARN @ant-design/ icons-vue@2.0.0需要vue-template-compiler@>=2.5.0的对等体，但是没有安装。您必须自己安装对等依赖项。

>npm WARN optional SKIPPING OPTIONAL DEPENDENCY ：这种提示不用管




npm install vue-template-compiler

npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.1.3 (node_modules\fsevents): 

>原因：
fsevents不在package.json里，但是仍然安装了，是因为你的系统是Windows系统，fsevents是苹果系统的可选依赖,你的项目有可能是团队项目，别人在他的mac上安装了fsevents相关依赖库，所以到这边你也就安装到你的windows上边了。你可以检查你的package.json 文件中是不是有fsevents相关依赖，删除即好！
如果没有，其他的npm包也会有依赖fsevents的！！！
这是warning错误，是因为mac下需要 fsevents，这里是在windows环境，所以可以忽略这个警告，对你没什么影响的。


访问指定的地址后
>提示

在模板编译器不可用的情况下，您使用的是仅运行时构建的Vue。要么将模板预编译为呈现函数，要么使用编译器包含的构建


>提示：错误

>原因：使用了没有注册的组件

未知的自定义元素:<a-config-provider> -您正确地注册了组件吗?对于递归组件，请确保提供“name”选项



#### 注意
>需要注意的是，样式文件需要单独引入

#### 引入后遇到的问题
>Failed to resolve loader: less-loader
提示需要安装  less-loader

### 使用这个库遇到的问题样式总是引入不过来
>错误：在页面上查看没有对应 css

>解决：少相应的css样式文件
>问题：main.js怎么引入 assets/css/main.css文件






## 二、iView  基本就是模仿 Ant-Design 功能还少
[跳转到官网](https://iviewui.com/docs/guide/install)



## 三、