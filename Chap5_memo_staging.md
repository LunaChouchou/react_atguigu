## React路由
- 对SPA应用的理解
  - 课件doc
  - single page web app
- 对路由的理解
  - 路由链接 `<a>`  引起路径发生变化 不带.html的
    - /home 组件 不会引起页面跳转画面刷新
    - /home.html home页面
  - 前端路由工作原理
    - 更改路径→路由在监测地址栏路径的变化→展示对应组件
    - 路由拿到的不是url全体 而是ip后边的path /home /about之类的
  - 一个路由就是一个映射关系
    - key=value
    - path=function/component
  - 后端路由
    - 所需服务器文件夹里的server.js 13行 后端路由
    - path对function的映射
  - 前端路由
    - component
- 前端路由原理
  - 浏览器历史记录 history
  - E:\03_React\react全家桶资料\06_其他\前端路由的基石_history.html
  - BOM 浏览器对象 有历史记录
    - 方法一 createBrowserHistory
    - window是BOM对象，document是DOM对象
    - window.history 可以对浏览器路径、历史记录等进行操作 但是原生API不太好用
    - history.js库 借助这个库进行操作
    - 自己写的let history 最终操作BOM上的history
    - history.push 往历史记录里添加一个记录
    - onclick return false 就不会跳转到href写的url
      - 豆知识：onclick后如果返回false的话event的默认动作不会发动
    - listen(location) 监听路径
      - location路径发生变化输出最新路径
    - history.replace 覆盖栈顶的记录
    - 后退前进可以用代码 控制history
      - history.goBack, history.goForward
  - 方法二hash history 
    - 方法一的区别：有#
    - onclick="push('/test2')"
      - 页面URL.html#
      - →页面URL.html#/test2
    - 像锚点跳转 写法
      - 不会引起画面刷新，会留下历史记录
      - a href="#demo"
      - 页面URL#
      - →页面URL.html#demo
- 路由的基本使用
  - react-router 库
    - web
    - native ReactNative React原生应用
    - any
  - react-router-dom库
    - react-xxxx react的插件库
    - 印记中文 https://react-router.docschina.org/web/guides/philosophy
    - 脚手架里不自带 多功能应用才需要
      - prop-types, react-router-dom
    - yarn add react-router-dom
      - yarn add vue-router
    - 5版本（最新6）
      - npm i react-router-dom@5
  - 使用路由的套路
    - 导航选项=路由链接
    - 前端路由器监测
    - 要引入的东东
      - Link
        - 路由链接的to之后的组件不分大小写 全写小写 不写. to="/about"
        - Link要写在Router标签内部
      - Router
        - BrowserRouter
        - HashRouter
      - Route
        - Route要写在Router标签内部
    - 编写路由链接
      ```js
      <BrowserRouter>
        <Link className="list-group-item" to="/about">About</Link>
        <Link className="list-group-item" to="/home">Home</Link>
      </BrowserRouter>
      ```
    - 注册路由
      - 定义路由连接和组件的映射关系
      - `<Route/>`
      - 属性path="/about" component={About}
        - component小写的
      ```js
      <BrowserRouter>
        <Route path="/about" component={About}/>
        <Route path="/home" component={Home}/>
      </BrowserRouter>
      ```
    - 整个应用要用同一个路由器管理
    - 两个(Browser/Hash)Router标签之间没有交流
    - →用一个Router标签 写在index.js里 写在App组件的外侧
- 路由组件与一般组件
  - README.md 三
  - HashRouter URL会出现#
    - #后面的东西叫hash值 锚点值 不会作为资源发送给服务器 都属于前台资源
  - <Home/>是一般组件
  - component={Home}是路由组件 靠路由进行匹配 匹配上了展示Home组件
    - 路由组件不放在components文件夹里 放在pages文件夹里（注意引入的地址需要修改）
    - 路由组件会受到路由器传递的3个props信息 不能写在标签里
      - history
      - location
      - match
  - README.md 四
    - props里的history.location和props里的location是同一个东西
- Router5查漏补缺（此处学习了Router6）
  - BrowserRouter和HashRouter的区别
  - Swtich
  - NavLink
  - Redirect
  - setState
- BrowserRouter与HashRouter
  - README.md 十三
  - 改变路径会留下历史记录
  - 原理
    - BrowserRouter用的H5浏览器自带的history对象 react对他二次封装（这个history不是路由里的那个this.props.history）
    - HashRouter 用的哈希值
      - #后的东西不认为是请求资源的路径 不会发送给服务器
      - 锚点能形成历史记录 不会发送给服务器 
      - 对老版本兼容性更好
  - params和search的path会带参数 state不带 hash有#
  - 路由的state参数
    - BrowserRouter刷新后history仍然存在
      - state保存在history里
      - props里的history.location.state 就是 location.state
    - HashRouter不行
      - history没了 state没保存