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
  background: url('~@/assets//images//bg.jpg') // src/assets/images
  background: url('cdn链接')
  background: url('/img/bg.jpg') // 引入public中图片
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

#### useMemo(()=>{},[])

类似computed属性

```jsx
// 关联更新数据的方法触发
// 触发条件：父组件更新时，不依赖数据的触发也会更新
// 父组件通过触发方法更新数据，只有依赖的数据的触发时进行更新=》useMemo()

// 默认情况下onClick方法会触发getNum方法，onChange也会触发getNum方法
// 阻止不依赖数据的更新：给getNum方法添加useMemo()，只有count更新时，关联的getNum方法才会更新，value更新时不触发getNum方法

export default testPage(){
  const [count, setCount] = useState(0)
  const [value, setValue] = useState('0')

  const getNumMemo = useMemo(()=> {
    console.log('data update);
    return count*100
  },[count])

  return (
    <>
    <h3>父组件</h3>
    <div>getNumMemo:{getNumMemo}</div>
    <button onClick={()=>setCount(count+1)}>+1</button>
    <input value={value} onChange={(e)=> setValue(e.target.value)}/>
    </>
  )

}

```

#### Memo(compoent)

> 高阶组件

```jsx
// 父子组件
// 触发条件：父组件更新时，不依赖数据的子组件也会更新
// 父组件通过触发方法更新数据时，不依赖的数据的子组件不更新=》Memo()

// 默认情况下onClick触发count更新时，onChange方法触发value也会更新子组件
// 解决useState的触发：将不依赖value数据的子组件用memo包裹，只有count更新，子组件更新

export default testPage(){
  const [count, setCount] = useState(0)
  const [value, setValue] = useState('0')

  return (
    <>
    <h3>父组件</h3>
    <div>getNumMemo:{getNumMemo}</div>
    <button onClick={()=>setCount(count+1)}>+1</button>
    <input value={value} onChange={(e)=> setValue(e.target.value)}/>
    <Child count={count}></Child>
    </>
  )

}

```
> 给不依赖触发数据的组件添加高阶组件Memo

```jsx
// 子组件
import {Memo} from 'react'
const Child = ({count}){
  const show = ()=>console.log('rendered child component')

  return (
    <>
      <h3>child component</h3>
      <div>{show()}</div>
      <div>{count}</div>
    </>

  )
}
export default Memo(Child)
```

#### useCallback(()=>{},[])

```jsx
// 父子组件

// 默认情况下子组件onClick触发不依赖父组件数据时，父组件关联方法更新，父组件触发任意方法，子组件每次都更新
// 解决useState的触发：将不依赖value, count数据的子组件不更新，给触发方法添加useCallback
// 如果子数据依赖count变化时，给useCallback添加依赖项

export default testPage(){
  const [count, setCount] = useState(0)
  const [value, setValue] = useState('0')

  const updateCount = useCallback(()=>{
    console.log('parent componet update')
  })

  return (
    <>
    <h3>父组件</h3>
    <div>getNumMemo:{getNumMemo}</div>
    <button onClick={()=>setCount(count+1)}>+1</button>
    <input value={value} onChange={(e)=> setValue(e.target.value)}/>
    <Child updateCount={updateCount}></Child>
    </>
  )

}

```

```jsx
// 子组件
import {Memo} from 'react'
const Child = ({updateCount}){
  const show = ()=>console.log('rendered child component')

  return (
    <>
      <h3>child component</h3>
      <div>{show()}</div>
      <button onClick={updateCount}></button>
    </>

  )
}
export default Memo(Child)
```

### 路由、权限、动态、约定式（理想状态）

#### 组件结构

```text
├── config
  ├──config.js
  ├── routes.ts

├── pages
  ├──login
    ├──index.jsx
    ├──index.less

```

#### layout布局

```text
                layouts
                  ├
    ──────────────────────────────
    ├                             ├
components                      children
    ├                             ├               
  nav           ──────────────────────────────
                ├                             ├
        layouts/layouts2                ────────────────
                ├                       ├       ├       ├
        ───────────────────             pages   pages   pages
        ├                  ├             ├       ├        ├
  components           children          1       2       ...
        ├                  ├
        menu             pages
                          ├
              ──────────────────────────
              ├       ├       ├         ├
              page1  page2   page3    page4


```

**路由布局示例**

```js
export default [
  {
    path: '/'
    component: '@/layouts/base-layout',
    routes: [
      {path: '/login',component: '@/pages/login'},
      {path: '/reg', component: '@/pages/reg'},
      {path: '/', redirect: '/login'},
      {path: '*', layout: false, component: '@/pages/404'},
      {
        path: '/goods',
        wrapper: ['@/pages/auth'],
        component: '@/layouts/aside-layout',
        routes: [
          {path: '/goods', component: '@/pages/goods'},
          {path: '/goods/:id', component: '@/pages/goods/goods-detail'},
          {path: '/goods/:id/comment', component: '@/pages/goods/comment'},
          {path: '/goods/:id/comment/:cid', component: '@/pages/goods/comment-detail'}
        ]
      }
    ]
  },
  {path: '*', layout: false, component: '@/pages/404'}
]

```
注：配置路由组件的包装组件，通过包装组件可以为当前的路由组件组合进更多的功能。 比如，可以用于路由级别的权限校验

**页面布局示例**

- base-layout

```jsx
export default function BaseLayout (props){
  return (
    <>
      <Nav></Nav>
      <div>{props.children}</div>
    </>
  )
}
```

- asid-layout

```jsx
export default function AsideLayout (props){
  return (
    <>
      <Menu></Menu>
      <div>{props.children}</div>
    </>
  )
}
```

```jsx
import {Redirect} from 'umi'
// wrapper/auth.jsx
export default (props)=>{
  if(Math.random < .5) {
    return <div>{props.children}</div>
  } else {
    return <Redirect to={} />
  }
}

```

#### 页面跳转、参数接收

#### 声明式

**navLink/Link -v3**

```html
<NavLink
 activeStyle={{color: red}}
 to={{pathname: '/goods/3', search: '?a=2'}}></NavLink>

 <NavLink
 activeStyle={{color: red}}
 to={{pathname: '/goods/4', query: {a: 2}}}></NavLink>

```

**NavLink/Link -v4**

```html
<ul>
    <li><NavLink to="message" style={({ isActive }) => isActive ? { color: 'red' } : undefined}>Messages</NavLink></li>
    <li><NavLink to="tasks" className={({ isActive }) => isActive ? 'active' : undefined}>Tasks</NavLink></li>
    <li><NavLink to="blog">{({ isActive }) => <span className={isActive ? 'active' : undefined}>Blog</span>}</NavLink></li>
</ul>

```

#### 编程式

**v3**

- useLocation

hooks，获取 location 对象: `location.pathname`

- useParams

hooks，获取 params 对象。 params 对象为动态路由（例如：/users/:id）里的参数键值对

- useRouteMatch

获取当前路由的匹配信息:`useRouteMatch().params`

- useHistory

```jsx

export default function test(){
  const history = useHistory()
  const goLink = () => {
    history.push({
      pathname: '/goods/3',
      query: {b: 1}
    })
  }

  return (
    <>
    <button onClick={goLink}>编程式跳转</button>
    </>
  )

}

```
- withRouter

高阶组件，可以通过 withRouter 获取到 history、location、match 对象

```jsx
export defautl withRouter((history,location,match)=>{
  return (<></>)
})

```

**v4**

### mock













