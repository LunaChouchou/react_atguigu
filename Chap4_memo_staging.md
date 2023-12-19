## react ajax
- 手脚架配置代理_方法1
  - 用axios发送网络请求
  - `axios.get('').then()`
    - axios返回promise对象 包含成功结果/失败原因
    - then里定义两个函数 成功/失败了做什么
    - response 成功的返回值
    - respons.data 成功的结果
    - error 失败原因
  - 给一个服务器发送请求
    - 课件里的服务器用的Node.js和Express框架
    - app.use() 用了一个全局的中间件（应该是一个Listener）
    - app.get 后端配置的路由 （get方式）
    - app.listen 启动服务器
  - 开启服务器
    - node server1.js
    - 浏览器默认发送的请求是get
    - http://localhost:5000/students
  - 跨域问题
    - 同源策略的限制
    - 服务器和ajax请求都用的localhost 浏览器3000端口→服务器5000端口
      - 可以通过ajax发送请求 但是ajax会拦截本次响应
    - 通过代理解决
    - 中间人3000端口 转发请求给5000 
      - 因为中间人没有ajax引擎所以返回不会被拦截
  - 开启代理 react里有两种方式
    - ①（更简单）package.json里写proxy URL写到端口号为止 （=开一个位置在URL的代理）
      - `"proxy":"http://localhost:5000"`
      - 3000发给3000的请求都会转发给5000
        - 需要重启脚手架
      - 发送请求目标从5000/students改成3000/students 
        - 并不是所有给3000的请求都会发送到5000 只转发3000没有的资源
          - 发送请求给localhost:3000/index.html 返回的是3000自己public下的index.html
          - 3000有的资源3000就不会再转发了
- 手脚架配置代理_方法2
  - ②setupProxy.js
    - 可以配置多个代理
    - 需要重启脚手架
    - 不能写ES6 写CJS（Common JS） 不是前端代码 是给React用于加入Webpack配置里的文件
    - 导入库 React脚手架已经下载好了http-proxy-middleware库
    - 暴露对象 最新是暴露一个函数
      - 参数1：要把哪些前缀的请求转发给5000
        - /api1类似的东西都会走代理
      - 参数2：转发给谁
        - http://localhost:5000
      - 如果请求目标3000/students下没有目标数据 就走api1配置的这个地址 5000/students
      - changeOrigin最好改成true 服务器会认为请求方是代理后的域名
        - `request.get('Host')` 获取请求方域名
      - pathRewrite
        - 真实请求时帮你重写api1 必须要写
        - ^ 正则匹配
        - `request.url` 获取请求方地址
    - react载入setupProxy.js 扫描api1, api2 有就转发
- github搜索案例_静态组件
  - 复习流程
    - 复制代码，如果删除部分有id=，确认没有该id的样式
    - class替换成className
    - 检查style 改格式 `style='width: 100px'` → `style={{width:'100px'}}`
    - 复制.css 引入css
    - 引入bootstrap.css 
      - 成型的css最好放在public/css
      - 引入到index.html 用link标签
        - `<link rel="stylesheet" href="/css/bootstrap.css">`
        - /css表示网站根目录
    - 拆分组件 替换<组件/> import组件
    - 拆分样式 有新建index.css的引入css 有删除css的删除引入
    - 检查报错
      - target="_blank" 点击后新页标签打开
        - 必须同时用rel="noreferrer"  不向目标页面发送HTTP Referer头部信息
      - img标签必须有alt属性
- github搜索案例_axios发送请求
  - 按button时想要获得input的内容 用ref或写成受控组件/受控表单
  - 连续解构赋值
    - `const {keyWordElement:{value}} = this`
    - 只取出了keyWordElement.value keyWordElement没有
    - `let obj = {a:{b:{c:1}}}`
      - 取出c `obj.a.b.c`
      - 连续解构赋值形式取出c `const {a:{b:{c}}} = obj`
    - 连续解构赋值+重命名
      - `let obj2 = {a:{b:1}}`
      - 取出b `const {a:{b}} = obj2`
      - 改名b为data `const{a:{b:data}} = obj2`
  - 发送请求
    - import axios
    - 请求需要的信息：URL，method(get/post)，params
      - https://api.github.com/search/users?q=xxxxxxx
      - 会返回json
    - 课件里服务器 以防github拒绝请求 一个真实接口一个会返回既定数据的dummy 开在5000
    - 解决跨域问题的一劳永逸的方法 cors 后端加上一个特殊的响应头
      - github网站使用了cors解决了
    - 启动课件服务器
      - npm start （package.json里定义了内容为node xxx.js）
    - 用setupProxy.js方法 3000给3000发，3000有代理，转发给5000
    - /api1是前缀 必须写在域名后面
- github搜索案例_展示数据
  - 选择需要的信息 头像，id，主页URL……
  - response.data.items取出收到的json里的users数组
  - 复习：遍历时需要给最外侧标签加上key属性 
    ```javascript
    render
      return (
        div //最外侧1标签
          { //jsx代码
            map=>{
              return (
                div/ //遍历最外标签 需要key属性
              )
            }
          }
        /div
      )
    ```
  - 返回一个最外侧div 里面包裹多个小div
- github搜索案例_完成案例
  - List需要展示的内容：users, first初次渲染的欢迎词, loading搜索过程, err出错信息
  - 请求失败时返回的error是一个对象
    - 不能用来展示在页面上
  - 复习：jsx里不能写语句 只能写表达式
    - 不能写if判断 可以写三元表达式
  ```javascript
  this.props.updateAppState({isLoading:false,users:response.data.items}) //Header
  this.setState(stateObj) //App#updateAppState
  ```
    - 这样可以更新state里同名对象or添加新对象
  - 搜中文会返回空数组
- 消息订阅与发布机制_pubsub
  - 兄弟组件间如何通信
  - PubSubJS库 
    - 一个技术的仓库名和npm上的名字（小写字母）不一定一样
    - yarn add pubsub-js
  - https://github.com/mroderick/PubSubJS
    - import
    - token = subscribe('MY TOPIC',mySubscriber)
      - 在接受数据的组件里订阅消息 指定消息名&有人发布消息时的回调 回调给你传msg和data两个参数
      - 每次订阅时可以获得一个订阅的token 可以用来取消订阅之类的
        - id = setInterval/setTimeout 定时器的左侧可以得到一个定时器的编号
    - publish('MY TOPIC',数据) 发布消息
    - unsubscribe(token)
  - List组件刚放到页面上的时候就要订阅消息 用钩子订阅消息/取消订阅消息
    - componentDidMount订阅消息
    - componentWillUnmount取消订阅消息
  - 参数可以用_占位
- fetch发送请求（不知道也可以 使用率不高）
  - 原生ajax发送请求 用xhr
    - `const hxr = new XMLHttpRequest()`
    - xhr.open.send
  - jQuery发送请求
    - $. get
    - xhr的封装
    - 可能产生回调函数地狱
  - axios发送请求
    - promise风格的所以不会有回调函数地狱
    - 也是xhr的封装
    - 服务器端没有封装xhr 但是封装了http
      - jQuery和axios都需要下载 引入
  - fetch发送请求（不同于上面三种基于xhr的）
    - window上内置的 不是第三方库 不需要下载 promise风格
    - https://github.github.io/fetch/
    - https://segmentfault.com/a/1190000003810652
    - 关注分离
      - 联系服务器成功/失败
        - 成功：连接成功，得到任意返回值（可指定return后写的东西）
        - 失败：连接失败，断网之类，返回undefined
      - 获取数据成功/失败
    - response原型身上有一个json()方法 指定return response.json() 返回值是一个Promise实例对象
      - 07 index.jsx第33行 加个.then
        - promise要链式调用 json().thenになる
        - 看返回的是什么东西：pending状态的Promise实例、Promise实例、异常
          - Promise实例：？
          - 非Promise实例：？
          - TODO：没听太明白
        - 如果连接失败，想让他中断，就new一个空的Promise
      - 加个.catch 
        - 只要失败了都走这个catch
    - await是啥 线性处理
      - await右边一定要写Promise实例
      - 上面的函数写个async
        - await语句要配合async函数使用
        - Promise状态变化前暂停执行 跟Thread.sleep差不多吧
    - promise.json返回的是个对象 要用data.items取出来 不然会有maps()不可用的报错
- 总结github搜索案例
  - README.md 二