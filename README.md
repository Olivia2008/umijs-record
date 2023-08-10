## umijs3.0

### 安装

```bash
yarn create @umijs/umi-app
```

### 目录结构

```text
├── mock                  // mock文件所在目录，基于express
├── src                   // 源码目录
  ├── layouts/index.js    // 全局布局
  ├── pages               // 页面目录，里面的文件即路由
  ├── models              // 数据流
  ├── wrappers            // 权限管理
  ├── app.js              // 运行时配置文件
  ├── global.css          // 约定的全局样式文件，自动引入，也可以用global.less
  ├── global.js           // 可以在这里加入polyfill
├── .umirc.js             // umi配置，同config/config.js二选一
├── dist                  // 默认的build输出目录
├── publick               // 变通的数据资源目录和一些无需要打包的资源
├── .env                  // 环境变量

```

### 构建时配置

.umirc.ts

```js
export default defineConfig({
  /**
   * @name 开启 hash 模式
   * @description 让 build 之后的产物包含 hash 后缀。通常用于增量发布和避免浏览器加载缓存。
   * @doc https://umijs.org/docs/api/config#hash
   */
  hash: true,

  /**
   * @name 兼容性设置
   * @description 设置 ie11 不一定完美兼容，需要检查自己使用的所有依赖
   * @doc https://umijs.org/docs/api/config#targets
   */
  // targets: {
  //   ie: 11,
  // },
  /**
   * @name 路由的配置，不在路由中引入的文件不会编译
   * @description 只支持 path，component，routes，redirect，wrappers，title 的配置
   * @doc https://umijs.org/docs/guides/routes
   */
  // umi routes: https://umijs.org/docs/routing
  routes,
  /**
   * @name 主题的配置
   * @description 虽然叫主题，但是其实只是 less 的变量设置
   * 在src下创建global.less，更改根部的所有样式设置:root:root{--admin-color-primary:#399}
   * @doc antd的主题设置 https://ant.design/docs/react/customize-theme-cn
   * @doc umi 的theme 配置 https://umijs.org/docs/api/config#theme
   */
  theme: {
    // 如果不想要 configProvide 动态设置主题需要把这个设置为 default
    // 只有设置为 variable， 才能使用 configProvide 动态设置主色调
    "root-entry-name": "variable",
  },
  /**
   * @name moment 的国际化配置
   * @description 如果对国际化没有要求，打开之后能减少js的包大小
   * @doc https://umijs.org/docs/api/config#ignoremomentlocale
   */
  ignoreMomentLocale: true,
  /**
   * @name 代理配置
   * @description 可以让你的本地服务器代理到你的服务器上，这样你就可以访问服务器的数据了
   * @see 要注意以下 代理只能在本地开发时使用，build 之后就无法使用了。
   * @doc 代理介绍 https://umijs.org/docs/guides/proxy
   * @doc 代理配置 https://umijs.org/docs/api/config#proxy
   */
  // proxy: proxy[REACT_APP_ENV as keyof typeof proxy],
  proxy: {
    dev: {
      // localhost:8000/api/** -> https://preview.pro.ant.design/api/**
      "/api/": {
        // 要代理的地址
        target: "http://192.168.1.71:33353",
        // 配置了这个可以从 http 代理到 https
        // 依赖 origin 的功能可能需要这个，比如 cookie
        changeOrigin: true,
      },
    },
  },
  /**
   * @name 快速热更新配置
   * @description 快速刷新，可以保留组件state状态
   */
  fastRefresh: true,
  //============== 以下都是max的插件配置 ===============
  /**
   * @name 数据流插件
   * @@doc https://umijs.org/docs/max/data-flow
   */
  model: {},
  /**
   * 一个全局的初始数据流，可以用它在插件之间共享数据
   * @description 可以用来存放一些全局的数据，比如用户信息，或者一些全局的状态，全局初始状态在整个 Umi 项目的最开始创建。
   * @doc https://umijs.org/docs/max/data-flow#%E5%85%A8%E5%B1%80%E5%88%9D%E5%A7%8B%E7%8A%B6%E6%80%81
   */
  initialState: {},
  /**
   * @name layout 插件
   * @description 配置标题
   * @doc https://umijs.org/docs/max/layout-menu
   */
  title: "传承服务中心 - 控制台",
  /**
   * @name 标题icon
   * @description 配置icon 使用本地图片favicon.ico，请放到public中直接引用
   * @doc
   */
  favicon: "/favicon.ico",
  // 按需加载时指定的loading组件
  dynamicImport: {
    loading: "@/component/loading",
  },
  /**
   * @name 模版约定
   * @description 配置 react 组件树渲染到 HTML 中的元素 id，在pages下面创建documents.ejs
   */
  mountElementId: "app",
  layout: {
    locale: false,
    ...defaultSettings,
  },
  /**
   * @name moment2dayjs 插件
   * @description 将项目中的 moment 替换为 dayjs
   * @doc https://umijs.org/docs/max/moment2dayjs
   */
  moment2dayjs: {
    preset: "antd",
    plugins: ["duration"],
  },
  publicPath: process.env.NODE_ENV === "production" ? "./" : "/",
  /**
   * @name 国际化插件
   * @doc https://umijs.org/docs/max/i18n
   */
  locale: {
    // default zh-CN
    default: "zh-CN",
    antd: true,
    // default true, when it is true, will use `navigator.language` overwrite default
    baseNavigator: true,
  },
  /**
   * @name antd 插件
   * @description 内置了 babel import 插件
   * @doc https://umijs.org/docs/max/antd#antd
   */
  antd: {},
  /**
   * @name 网络请求配置
   * @description 它基于 axios 和 ahooks 的 useRequest 提供了一套统一的网络请求和错误处理方案。
   * @doc https://umijs.org/docs/max/request
   */
  request: {},
  /**
   * @name 权限插件
   * @description 基于 initialState 的权限插件，必须先打开 initialState
   * @doc https://umijs.org/docs/max/access
   */
  access: {},
  /**
   * @name <head> 中额外的 script
   * @description 配置 <head> 中额外的 script
   */
  headScripts: [
    // 解决首次加载时白屏的问题
    {
      src:
        process.env.NODE_ENV === "production"
          ? "./scripts/loading.js"
          : "/scripts/loading.js",
      async: true,
    },
    // 解决网络不好时富文本加载不出来的问题
    {
      src:
        process.env.NODE_ENV === "production"
          ? "./scripts/tinymce.min.js"
          : "/scripts/tinymce.min.js",
      async: true,
    },
  ],
  //================ pro 插件配置 =================
  presets: ["umi-presets-pro"],
  /**
   * @name openAPI 插件的配置
   * @description 基于 openapi 的规范生成serve 和mock，能减少很多样板代码
   * @doc https://pro.ant.design/zh-cn/docs/openapi/
   */
  openAPI: [
    {
      requestLibPath: "import { request } from '@umijs/max'",
      // 或者使用在线的版本
      // schemaPath: "https://gw.alipayobjects.com/os/antfincdn/M%24jrzTTYJN/oneapi.json"
      schemaPath: join(__dirname, "oneapi.json"),
      mock: false,
    },
    {
      requestLibPath: "import { request } from '@umijs/max'",
      schemaPath:
        "https://gw.alipayobjects.com/os/antfincdn/CA1dOm%2631B/openapi.json",
      projectName: "swagger",
    },
  ],
  mfsu: {
    strategy: "normal",
  },
  requestRecord: {},
});
```

### umi 内置

```bash
antd^4.0.0
antd-mobile^5.0.0
antd-mobile^2.3.4
基于 babel-plugin-import 做按需编译
```

- 查看某个插件的版本

```js
npm view @umijs/preset-react versions
```

- @umijs/preset-react:1.x
  需要升级到 2

- 按需要引入

```jsx
import {Button } from 'antd'
import {Button as V2Button} from 'antd-mobile-v2'
import {Button as V5Button} from 'antd-mobile-v5'

export default function indexPage() {
  return (
    <div>
      <Button type="primary">pc按钮</Button>
      <V2Button type="primary">mobile v2按钮</V2Button>
      <V5Button type="primary">mobile v5按钮</V2Button>
    </div>
  )
}
```

### 图片和资源引入

```css
.test1 {
  backgroud:url('../assets//images//bg.jpg') // 写死的图片会被转化为base64
  background: ulr('~@/assets//images//bg.jpg') // src/assets/images
  background: ulr('cdn链接')
  background: ulr('/img/bg.jpg') // 引入public中图片
}

```

### 样式模块化

#### 变量-全局

```css
@import "~antd/es/style/themes/default.less" .mylink {
  color: @primary-color;
  font-size: @font-size-base;
}
.title {
  background: #cornflowerblue;
}
```

```html
<div className="mylink">全局引入变量</div>
<div className="title">全局自定义变量</div>
```

#### 变量-局部

index.less

```css
@width: 100px;
@height: @width - 10px;

.header {
  width: @width;
  height: @height;
}
```

```jsx
// index.jsx
import styles frim './index.less'
export default function indexPage() {
  return (
    <div className={style.header}></div>
  )
}

```

#### 混合变量

```css
.bordered(@topW:1px, @bottomW:2px) {
  border-top: dotted @topW black;
  border-bottom: solid @bottomW black;
}

.a1 {
  color: #111;
  .bordered();
}
.a2 {
  color: red;
  .bordered(2px, 4px)
  border-bottom: solid 5px solid // 覆盖混合
}

```

#### 样式不添加 hash

```css
.bars_right {
  font-weight: bold;
  :global {
    .ant-btn {
      border: 0;
    }
  }
}
```

```html
<div className="{styles.bar_right}">
  <button className="{`ant-btn`}"></button> /*样式不添加hash*/
</div>
```

### hooks+函数式编写组件
