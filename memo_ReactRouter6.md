# ReactRouter6
- 加粗字体为ReactRouter5也适用的规则
- 一级路由
  - **防止二级路由的时候样式丢失 href="/bootstrap.css"不写./**
  - **需要高亮效果使用NavLink 不用Link**
        ```js
        <NavLink className="list-group-item" to="/about">About</NavLink>
        ```
  - Routes相当于原来的Switch
    - **Switch 匹配效率问题**
    - **一个匹配上了就不会继续扫描下面的了**
    - 注册路由的Route用Routes包住
      - 6必须包Routes 5可以不包Switch
    - component={About} → element={<About/>}
  - <Route caseSensitive>  匹配时是否区分大小写
- 重定向
  - 路径后没有/的话会出现警告
    - No routes matched location "/" 这个路径没有规则 没有对应的组件
    - **引入内置的Redirect用于重定向**
      - **<Redirect/>写在Route规则的下面**
      - **用来消除该警告？**
  - 改为引入Navigate
      ```js
      <Route path="/" element={<Navigate to="/about"/>}/>
      ```
    - 一定要写to属性
    - Navigate一被渲染 就会修改路径 视图就会被切换（redirect了）
  - Navigate也可以写replace属性 跳转模式
    - 默认push replace={false}
    - 改为get replace={true}
      - 历史记录会被redirect后的替代 所以回不到home页面？
  - **useState** from GPT
    - useState 返回一个包含两个元素的数组，其中包括当前状态值和用于更新该状态的函数。数组的第一个元素是当前状态的值，第二个元素是用于更新状态的函数。
    - ```const [sum,setSum] = useState(1)``` 用1去初始化state
- NavLink高亮
  - **高亮时是在点击时给NavLink加了一个样式类名active bootstrap自带的**
  - 给样式加!important可优先
  - 5的时候
    - **activeClassName="atguigu" 点的时候给你加上atguigu样式**
  - 想用自定义样式名需要把className的值写成一个函数
    ```js
    className={()=>{}}
    ```
    - 每次点击路由链接都会调用
    - 要有返回值 返回类名
    ```js
    className={(a)=>{}}
    ```
    - 参数a是一个对象 包含isActive属性
    ```js
    className={({isActive})=>{return isActive ? 'list-group-item atguigu' : 'list-group-item'}}
    ```
    - 同时应用list-group-item和atguigu两种格式
  - 规则太长可以提出一个共通化function
    - function computedClassName({isActive}){
    - 复习 from GPT：{isActive}对象解构语法 提取isActive属性在函数中使用
      - 传递同一个对象中的两个属性这么写：{ a, b }
- useRoutes路由表（6的新东东）
  - Routes 不同path和element的值叫做路由表
  - Hook有返回值 
  - const element = useRoutes([]) 数组 因为有多个注册路由Route
  - 每个元素的值都是对象
  - useRoutes
    - 一般做项目会建一个叫routes的文件夹 把规则移植进去
    - /routes/index.js
    - 复习：import目录下文件叫index.jsx可以省略包括index后的部分（index.js也可）
    - 豆知识：/routes/index.js下的export default []数组就叫做routes 引入后直接使用routes数组
  - <Routes>处改为{element} （只管一级（父）路由？注册了包括所有等级的路由）
- 嵌套路由
  - 加个children 数组 可以有复数个子link
  - 路由表子级link可以只写path:'news'
    - **5的时候/home/news**
  - Navlink下的to="news"
    - **5的时候/home/news**
      - →注册路由和Link跳转时都可以不用带父级路由路径了
        - 不带（6可）
          - 不能写/ 会匹配绝对路径
          - 可以写./ 不破坏当前路径
        - 带（56可）
          - 都写/
    - 5的时候没有内置路由表 所以不需要定位组件呈现在哪里（不是有{element}吗）
    - 引入Outlet <Outlet/> 类似vue里的Router-view （管一级以下的（子）路由？跟{element}放的地方一样但是作用只是指定位置？）
  - 在父级Link处加一个end就可以不让父级亮
- ①路由的params参数
  - 三级路由
  - 配置数据使用useState
  - 流程
    - 给Message.jsx传入messages数组 遍历展示消息title
    - 新建Detail组件
    - routes配置规则 给Message里添加子Detail（import Detail）
    - Message.jsx改路由链接（import Link, Outlet）
      - a改成Link
      - 放Outlet
    - 把参数带到Detail里
      - **3种传参 params, search, location.state**
      - 6写法有更新
  - ①-1params 携带参数
    - Message.jsx
      ```js
      <Link to="detail">
      ```
      ```js
      <Link to={`detail/${m.id}/${m.title}/${m.content}`}>
      ```
    - params定路由规则的时候要占位 声明接收
      ```js
      path:'detail/:id/:title/:content'
      ```
    - 这样path会带上detail/id/title/content
  - ①-2Detail接收参数
    - 5的时候
      - **路由组件props里自带history, location, match**
      - **this.props.上面三个**
    - 函数式组件没有this 借助hook接收参数
      - 引入useParams useParams()接收
      - 还有一个hook叫useMatch
        - **5里取出参数 this.props.match.params**
        - useMatch('/home/message/detail/:id/:title/:content') 把路径写好了给你match
        - 这里面就有params可以取出
        - useMatch().params
- ②路由的search参数
  - 用得不多
  - **5的时候 search的参数不需要在路由表占位** 6也不需要
  - ②-1Message.jsx传参携带search参数 和query很像
      ```js
      <Link to={`detail?id=${m.id}&title=${m.title}&conetent=${m.content}`}>
      ```
    - path带参数
  - ②-2Detail.jsx接收参数
    - 引入useSearchParams useSearchParams() 不是一个对象
      - 类似useState 需要通过解构一个数组去接收
      - const [search,setSearch] = useSearchParams() 分别为search参数和更新search参数的方法
      - search不是一个直接能用的对象 要用search.get('id')取出值
      - setSearch('id=008&title=哈哈&content=嘻嘻') 更新参数
    - 还有一个hook叫useLocation
      - useLocation().search可以取出search
- ③路由的state参数
  - 5的时候
    - **把to写成一个对象 包含pathname、state对象**
  - 6的时候
    - ③-1属性to="detail"目的地 属性state={{}}对象
    - ③-2接收时用useLocation 输出location对象 里面包含pathname, search, state
      - const {state:{id,title,content}} = useLocation() （使用了连续解构赋值 取出location对象里的state对象）
  - 总结
    - params
      - 5
        - 注册路由时需要占位 在Route path="/home/message/detail/:id/:title"
        - Link写法`/home/message/detail/${msgObj.id}/${msgObj.title}`
          - 不可省略父级路由
        - 接收 const {id,title} = this.props.match.params 
      - 6
        - 注册路由时需要占位 在路由表  path:'detail/:id/:title/:content'
        - Link写法`detail/${m.id}/${m.title}/${m.content}`
          - 省略了父级路由的写法
        - 接收 const { id, title, content } = useParams()
    - search
      - 5
        - 注册路由时不需要占位 在Route
        - Link写法`/home/message/detail/?id=${msgObj.id}&title=${msgObj.title}`
          - 不可省略父级路由
        - 接收 const {search} = this.props.location，querystring.parse(search.slice(1))
      - 6
        - 注册路由时不需要占位 在路由表
        - Link写法`detail?id=${m.id}&title=${m.title}&conetent=${m.content}`
          - 省略了父级路由的写法
        - 接收 const [search,setSearch] = useSearchParams()，search.get('id')
    - state
      - 5
        - 注册路由时不需要占位 在Route
        - Link写法{pathname:'/home/message/detail',state:{id:msgObj.id,title:msgObj.title}}
          - 不可省略父级路由？
        - 接收 const {id,title} = this.props.location.state || {}
      - 6
        - 注册路由时不需要占位 在路由表
        - Link写法 to="detail" state={{id:m.id,title:m.title,content:m.content,}} （除了这条to和state是分开的属性，其他的都是Link的to属性内容）
          - 省略了父级路由的写法？
        - 接收 const {state:{id,title,content}} = useLocation()
- 编程式路由导航
  - 除了点击链接怎么渲染 
    - Link, NavLink, 最终变成a标签 点击时进行路由跳转
    - Navigate 有机会渲染到页面时引起视图切换
    - 5的时候 利用路由组件实例对象this.props.history 6没有this
      - goforward，goback之类的
    - 回调 直接在回调函数里写Link，NavLink不渲染
  - 利用hook useNavigate 内置组件 渲染到组件中 视图切换
  - 调用useNavigate()函数
    - navigate('/about') 跳转到path/about 相当于Link的to
    - navigate('detail') 跳转到子路由 不带参数的
    - navigate('detail',{配置对象})
      - 配置对象可以写replace、state
      - 不能写search、params，只能直接拼在路径里
  - 后退前进怎么做
    - 把Header做成一般组件 放在components里
    - **5的时候只有路由组件才有history, match, location属性，实现路由相关API调用**
      - **withRouter 把一般组件加工 带有路由组件属性**
    - 6里用useNavigate就可以用路由属性了
      - navigate(-1)后退 1前进
  - 最新看doc https://reactrouter.com/en/main/hooks/use-navigate#usenavigate
- 余下课时
  - useInRouterContext
  - useNavigationType
  - useOutlet
  - useResovledPath