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
  /*
   *内置插件配置
   */
  plugins: [
    "@umijs/plugins/dist/dva",
    "@umijs/plugins/dist/request",
    "@umijs/plugins/dist/model",
  ],
  /*
   *
   */
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

类似 computed 属性

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

> 给不依赖触发数据的组件添加高阶组件 Memo

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

#### layout 布局

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
export default function BaseLayout(props) {
  return (
    <>
      <Nav></Nav>
      <div>{props.children}</div>
    </>
  );
}
```

- asid-layout

```jsx
export default function AsideLayout(props) {
  return (
    <>
      <Menu></Menu>
      <div>{props.children}</div>
    </>
  );
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
export default function test() {
  const history = useHistory();
  const goLink = () => {
    history.push({
      pathname: "/goods/3",
      query: { b: 1 },
    });
  };

  return (
    <>
      <button onClick={goLink}>编程式跳转</button>
    </>
  );
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

- useMatch

```JS

const match = useMatch('/comp/:id')
// match
{
  "params": {
    "id": "paramId"
  },
  "pathname": "/comp/paramId/",
  "pathnameBase": "/comp/paramId",
  "pattern": {
    "path": "/comp/:id",
    "caseSensitive": false,
    "end": true
  }
}
```

- useLocation

```jsx
{
  "pathname": "/path/",
  "search": "",
  "hash": "",
  "state": null,
  "key": "default"
}
```

> 🚨 推荐使用 `useLocation`, 而不是直接访问 `history.location.` 两者的区别是 `pathname` 的部分。 `history.location.pathname` 是完整的浏览器的路径名；而 `useLocation` 中返回的 `pathname` 是相对项目配置的 base 的路径
> 🚨 举例：项目如果配置 `base: '/testbase'`, 当前浏览器地址为 `https://localhost:8000/testbase/page/apple` > `history.location.pathname` 为 `/testbase/page/apple` > `useLocation().pathname`为 `/page/apple`

- useParams

```js
// 路由配置 /comp/:id
// 当前 location /comp/paramId

const params  = useParams();
// params
{
  "id": "paramId"
}
```

- useSearchParamms

```js
// 当前 location /comp?a=b;
const [searchParams, setSearchParams] = useSearchParams();
searchParams.get("a"); // b
searchParams.toString(); // a=b

setSearchParams({ a: "c", d: "e" }); // location 变成 /comp?a=c&d=e
```

### mock

模拟延时：roadhog-api-doc

```js
import {delay} from 'roadhog-api-doc
export default delay({
  '/api/user': {id: 12345, name: 'Olivia'}
}, 2000)

```

**v4**

- mock 使用，默认 get 请求

```js
export default {
  // 默认get方法
  "/api/users": { id: 10000, name: "Olivia" },
  "/api/users/1": [
    { id: 100001, name: "foo" },
    { id: 100002, name: "woo" },
  ],
  // 后面可接自定义函数
  "POST /api/login": (req: any, res: any) => {
    const { username, password } = req.body;
    if (username === "Olivia" && password === "abc123") {
      res.send({
        code: 200,
        data: {},
        message: "success",
      });
    } else if (username === "admin" && password === "admin123") {
      res.send({
        code: 200,
        data: {},
        message: "success",
      });
    } else {
      res.send({
        code: 201,
        message: "failed",
      });
    }
  },
};
```

- 可以用 roadhog-api-doc 模拟请求延时

```js
import { delay } from "roadhog-api-doc";
export default delay(
  {
    "/api/user": { id: 12345, name: "Olivia" },
  },
  2000
);
```

- mockjs

```js
import mockjs from "mockjs";

export default {
  // 使用 mockjs 等三方库
  "GET /api/tags": mockjs.mock({
    "list|100": [{ name: "@city", "value|1-100": 50, "type|0-2": 1 }],
  }),
};
```

mockjs 模拟条数数据

```js
import { delay } from "roadhog-api-doc";
import mockjs from "mockjs";

export default delay(
  {
    "/api/list": (req, res) => {
      const { page = 1, limit = 3 } = req.body;
      const totalPage = 3;
      const lastPageLimit = 2; // 尾页条数
      const total = limit * (totalPage - 1) + lastPageLimit;
      res.send({
        code: 200,
        data: {
          page,
          limit,
          total,
          ...mockjs.mock({
            [`data|${page > totalPage ? lastPageLimit : limit}`]: [
              {
                "id|+1": 1,
                create_at: "@date('yyyy-MM-dd HH:mm:ss')",
                "type_str|1": ["aaaaaa", "bbbbbbb", "cccc", "dddddddd"],
                name: function () {
                  return [
                    mockjs.mock('@datetime("MMdd")'),
                    mockjs.mock("@country()"),
                    this.operator,
                  ].join("-");
                },
                path: "http://www.baidu.com",
                operator: "@cname",
                "status|1": ["0", "2", "3", "4"],
              },
            ],
          }),
        },
      });
    },
  },
  2000
);
```

### 请求配置

#### useRequest()

> 需要安装`@umijs/hooks`

第一种：`const {data, error, loading, run, cancel, fetches, loadingMore, loadMore} = useRequest('/api/user')`
第二种：`const {data, error, loading, run} = useRequest('/proxy/aaaaaaa')` 反向代理
第三咱：`const { data, error, loading, run } = useRequest((userId)=> `/api/userInfo/${userId}`);`
第四种：

```js
import { useRequest } from "@umijs/hooks";
const { data, error, loading, run } = useRequest(
  {
    url: "/api/login",
    method: "post",
    data: {
      username: "aaa",
      password: "123aaa",
    },
  },
  {
    manual: true,
  }
);
```

**options 属性**

- 手动请求 manual
  `manual`为`true`阻止初始化默认请求，只有触发`run`时才会开始执行。

  ```jsx
  import { useRequest } from '@umijs/hooks';
  ​
  export default () => {
  const { run, loading } = useRequest(changeUsername, {manual: true})

  return (
    <Button onClick={() => run('new name')} loading={loading}>
       Edit
    </Button>
    )
  }
  ```

- 轮洵 poilingInterval

自动定时发起网络请求

```jsx
import { useRequest } from '@umijs/hooks';
​
export default () => {
  const { data } = useRequest(getUsername, { pollingInterval: 1000, pollingWhenHidden: false })
​
  return <div>Username: {data}</div>
}
```

同时通过设置`pollingWhenHidden`，我们可以智能的实现在屏幕隐藏时，暂停轮询。等屏幕恢复可见时，继续请求，以节省资源。为 false 时屏幕不可见时，轮洵暂停

当然你也可以通过`run/cancel`来手动控制定时器的开启和关闭。

- 并行请求 fetchKey

并行请求有几个特点： 1)删除 n 个不同的用户，则需要维护 n 个请求状态。 2)多次删除同一个用户，则只需要维护最后一个请求。

`useRequest` 通过设置 `fetchKey `，即可对请求进行分类。相同分类的请求，只会维护一份状态。不同分类的请求，则会维护多份状态。在下面的代码中，我们通过 `userId` 将请求进行分类，同时我们可以通过 `fetches[userId]` 拿到当前分类的请求状态！

```jsx
export default () => {
  const { run, fetches } = useRequest(deleteUser, {
    manual: true,
    fetchKey: id => id, // 不同的 ID，分类不同
  });
​
  return (
    <div>
      <Button loading={fetches.A?.loading} onClick={() => { run('A') }}>删除 1</Button>
      <Button loading={fetches.B?.loading} onClick={() => { run('B') }}>删除 2</Button>
      <Button loading={fetches.C?.loading} onClick={() => { run('C') }}>删除 3</Button>
    </div>
  );
};
```

- 防抖 debounceInterval&节流 throttleInterval

在下面的例子中，无论调用了多少次`run` ，只会在输入停止后，发送一次请求。

```jsx
import { useRequest } from '@umijs/hooks';
​
export default () => {
  const { data, loading, run, cancel } = useRequest(getEmail, {
    debounceInterval: 500,
    manual: true
  });
​
  return (
    <div>
      <Select onSearch={run} loading={loading}>
        {data && data.map(i => <Option key={i} value={i}>{i}</Option>)}
      </Select>
    </div>
  );
};
```

- 缓存 & SWR & 预加载(cacheKey)

在 SWR 场景下，我们会对接口数据进行缓存，当下次请求该接口时，我们会先返回缓存的数据，同时，在背后发起新的网络请求，待新数据拿到后，重新触发渲染

```jsx
const { data, loading } = useRequest(getArticle, {
  cacheKey: "articleKey",
});
```

> 🚨 注意：同一个 cacheKey 的数据是全局共享的

- 屏幕聚焦重新发起请求 refreshOnWindowFocus

- 分页 paginated

`useRequest` 通过配置 `paginated = true` ，即可进入分页模式，自动帮你处理表格常见逻辑，同时我们对 `antd Table` 做了特殊支持

```jsx
import {useRequest} from '@umijs/hooks';
​
export default () => {
  const [gender, setGender] = useState('male');
  const { tableProps } = useRequest((params)=>{
    return getTableData({...params, gender})
  },
  {
    paginated: true,
    refreshDeps: [gender]
  });
​
  const columns = [];
​
  return (
    <Table columns={columns} rowKey="email" {...tableProps}/>
  );
};

```

- 加载更多 loadMore

```jsx
const { data, loading, loadMore, loadingMore } = useRequest(
  (d) => getLoadMoreList(d?.nextId, 3),
  {
    loadMore: true,
    cacheKey: "loadMoreDemoCacheId",
    fetchKey: (d) => `${d?.nextId}-`,
  }
);
```

- loadingDelay

通过设置 `loadingDelay` ，延迟 `loading` 变为 `true` 的时间，当请求很快响应时，可以有效避免 `loading` 变化导致的抖动。

### 键盘监听包-keymaster

### dva

#### model 设置

> 等同 redux
> 所有状态管理数据放在 model 文件夹下
> state:数据状态
> reducers: 修改同步操作
> effects: 修改异步操作，action 类型，第一个参是 action,第二个参是 options{put, call,select}, put 触发 action,call 调用异步逻辑，select 用于从 state 中获取数据
> subscriptions: 自定义监听事件 fn({dispatch, history}),

**model 在项目全局中**

- 目录结构

```text
├── pages
├── model
  ├── global.js
```

```js
// model/global.js
export default {
  namespace: 'global',
  state: {
    title: 'global hello world',
    text: 'global text',
    login: fasle,
    a: 'global a'
  },
  reducers: {
    setText(state) {
      return {
        ...state,
        text: 'set global text'
      }
    },
    // 同步修改传参
    setTtile(state, action) {
      return {
        ...state,
        title: `set global title:${action.payload.a}/${actiono.payload.b}`
      }
    },
    signIn(state, action)=>({
      ...state,
      login: true
    })
  },
  // 异步修改
  effects: {
    *getLogin(action, {put, call, select}){
      // 发送异步请求
      const res = yield call(request, '/api/login', {
        method: 'post',
        data: {
          username: action.payload.username,
          password: action.payload.password
        }
      })
      // 将异步请求数据放进同步操作reduces.signIn中
      yield put({type: 'signIn', payload: res})
    }
  }
}
```

**model 在对应的组件中**

- 目录结构

```text
├── pages
  ├── dva
    ├── models
      ├── a.js
      ├── b.js
    ├── index.jsx
    ├── model.js
```

- 只有一个模块数据

```js
// model.js
export default {
  namespace: "dva",
  state: "only one model state",
};
```

- 有多个模块数据

```js
// models/a.js
export default {
  namespace: 'a',
  state: {
    a: 'part dva state a'
  }
}

// models/b.js
export default {
  namespace: 'b',
  state: {
    b: 'part dva state b'
  },
  reducers: {
    setDataB(state) {
      return 'part page dva state b'
    }
  }
}
```

#### 与组件连接 connect

```jsx
import {connect} from 'umi'
const mapModelToProps = (models) => {
  return {
    title: models.global.title,
    text: models.global.text,
    login: models.global.login,
    globalA: models.global.a,
    // 获取局部model数据
    partdva: models.dva,
    parta: models.a,
    partb: models.b
  }
}

@connect(mapModelToProps)

export default testPage(props){

  const handleGlobalClick = (props)=> {
    props.dispatch({
      type: 'global/setTtile',
      payload: {a: 'global aa', b: 'global bb'}
    })
  }

  const handlePartClick = (props)=> {
    props.dispatch({
      type: 'a/setDataB',
    })
  }

  const handleEffectsClick = (props) => {
    props.dispatch({
      type: 'global/signIn',
      payload: {username: 'admin', password: 'admin123'}
    })
  }

  return (<>
  <h3>获取全局state</h3>
  <div>title:{props.title}</div>
  <div>text:{props.text}</div>
  <div>globalA:{props.globalA}</div>
  {props.login ? <div>已登录</div> : <div>未登录</div>}
  <button onClick={handlGlobalClick(props)}></button>

  <h3>获取局部state</h3>
  <div>model中数据:{props.partdva}</div>
  <div>models/a中数据:{props.parta}</div>
  <div>models/b中数据:{props.partb}</div>
  <button onClick={handlePartClick(props)}>修改models/a/state.b</button>

  <h3>发送异步请求</h3>
  <button onClick={handleEffectsClick(props)}>发送effects请求</button>
  </>)
}
```

### 路由权限

#### 动态添加路由

模拟一个有权限登录的 mock 接口

```js
// mock-login.js
export default {
  "GET /api/auth": (req, res) => {
    res.send({
      isLogin: true,
    });
  },
};
```

mock 一个动态获取的路由接口

```js
export default {
  "GET /api/menus": (req, res) => {
    res.send([
      {
        path: "/",
        component: "layouts/aside-layouts",
        routes: [
          {
            path: "/goods",
            title: '商品'
            component: "layouts/aside-layouts",
            routes: [
              { path: "/goods/:id?", component: "pages/goods/godds-detail" },
              { path: "/goods/:id/comment", component: "pages/goods/comment" },
              {
                path: "/goods/:id/comment/:cid",
                component: "pages/goods/comment/comment-detail",
              },
              { component: "pages/404" },
            ],
          },
        ],
      },
    ]);
  },
};
```

封装一个 render 函数

```js
// src-app.js
import { request, history } from "umi";
// 存放动态路由
let routesData = [];
// 处理动态路由，给每条路由添加exact:true, component:require()引入
// 注意点：1)动态路由读取后，跳转后不显示，需要关闭mfsu:{}
// 2) 子路由不跳转，除了layout组件，其他需要添加exact
// 3)数据里面不可以有require，数据需要过滤成require(非空字符拼接+变量)
// 4）document.ejs报错，需要require拼接时找到index.jsx，目前umi3有这个问题
const filterRoutes = (routesData) => {
  routesData.map((item) => {
    if (item.routes && item.routes.length > 0) {
      filterRoutes(item.routes);
    } else {
      item.exact = true;
    }
    if (!item.redirect) {
      if (item.component.includes("404")) {
        item.component = require("@/" + item.component + ".jsx").default;
      } else {
        item.component = require("@/" + item.component + "/index.jsx").defalut;
      }
      if (item.wrappers && item.wrappers.length > 0) {
        item.wrappers.map((str, index) => {
          item.wrappers[index] = require("@/" + str + ".jsx").default;
        });
      }
    }
  });
};
export function patchRoutes({ routes }) {
  // routes是原本的白名单路由
  filterRoutes(routesDta);
  routesData.map((item) => routes.push(item));
}

export const render = async (oldRender) => {
  const { isLogin } = await request("/api/auth");
  if (!isLogin) {
    history.push("/login");
  } else {
    routesData = await request("/api/menus");
  }
  oldRender();
};

// 监听路由变化添加埋点
export function onRouteChange({ matchedRoute, location, routes, action }) {
  console.log("routes", routes); //
  console.log("matchedRoutes", matchedRoutes); // 当前匹配的路由及子路由
  console.log("location", location); // location及其参数
  console.log("action", action); // 当前跳转执行的操作
  // 系统的title名字
  document.title =
    matchedRoutes[matchedRoutes.length - 1].route.title || "hello world";
}
```

#### 拦截器

```js
export const request = {
  // timeout: 1000, // 延时
  // errorConfig: {}, // 错误处理
  // middlewares: [], // 使用中间件
  requestInterceptors: [
    (url, options) => {
      options.headers = {token: ''aeaf23442rqfaafa3455};
      return {url, options}
    }
  ],
  responseInterceptors: [
    (res, options) => {
      return res
    }
  ]
}

```

组件中调用

```jsx
import { useRequest } from "umi";
export default function testPage(props) {
  const { data, error, laoding } = useRequest({
    url: "http://localhost:3000/api/goods",
  });
  if (error) {
    return <div>faild to load</div>;
  }
  if (loading) {
    return <div>...loading</div>;
  }
  return <div>{JSON.stringify(data)}</div>;
}
```

### umi 内置插件的配置

### IOS 图片不显示

**有两种解决办法**

> 1、去掉`postcss.config.js--->"postcss-viewport-units": {},`
> 2、给 img 外层包一个父标签 div/figure,设置父标签的 width:200px, height:200px,让 img 直接继承父级 width:100%,height:100%
