## 前端

### 用户体验
  - #### 1、[前端性能优化](./前端性能优化)
    - 资源压缩合并，减少http请求  
    - 非核心代码异步加载
    - 利用浏览器缓存
    - 静态资源CDN加速
    - DNS预解析
    - Service Worker
    - Web Workers
    - 其他优化方式

  - #### 2、vue等单页项目 首屏加载白屏是怎么处理的
    - 交互设计
      - 给个loading动画，过度资源加载过程
      - 设计占位图和loading，页面加载完成后替换
      - 一屏可视组件优先加载

    - 代码优化
      - 考虑对于页面的组件异步加载
      - webpack打包优化，减小打包文件体积大小
      - vuejs路由的懒加载，减少首屏加载资源
      - 配置vue-cli，利用webpack插件完成模板预加载
      - 考虑服务器渲染首屏骨架页面，或者使用服务器渲染策略ssr
      - 首页数据初始化和js包同时加载
      - js拆包

### JavaScript
  - #### 1、js数据类型
    - 基本类型：String、Number、Boolean、Undefined、Null
    - 引用类型：Array、Object
    - 特殊类型：Symbol（ES6 唯一值）

  - #### 2、闭包
    - 闭包属于静态作用域，父级函数被销毁后，返回的子函数的`[[scope]]`中仍然保留对父级变量的对象和作用域链，因此可以继续访问父级的变量对象

    - 直译：一个函数内部返回另一个函数，而这个内部函数使用了父级的变量，在这个函数被调用时即产生了闭包。

    - 闭包产生的问题：
      - 形成闭包的函数的内部变量不会被垃圾回收机制所销毁
      - 多个子函数的`[[scope]]`都是同时指向父级，是完全共享的。因此当父级的变量对象被修改时，所有子函数都受到影响。

  - #### 3、this关键字
    - this是JavaScript的一个关键字，通常情况下指代调用它的那个对象，随着使用的场合不同，它的值会发生改变。
      - 在浏览器中默认情况下指代window对象
      - 在自执行函数中默认指代window对象
      - 在一般函数中默认指代函数本身
      - 通过new关键字生成的实例对象，this会指向实例本身
    - 通过使用`call`、`apply`、`bind`可以手动改变this的指代对象

    - call、apply、bind的主要区别 传参的方式不一样
      ```js
        let obj = { name: 'xm' }
        function test (argu, argu2) {
          let name = 'tt'
          console.log(this.name + ':' + argu + '、' + argu2)
        }
        test.call(obj, 'sleep', 'coding')
        test.apply(obj, ['sleep', 'coding'])

        // bind 返回的是一个函数
        // 用法1：
        let test1 = test.bind(obj)
        test1('sleep', 'coding')
        // 用法2：
        let test2 = test.bind(obj, 'sleep', 'coding')
        test2()
      ```

  - #### 4、原型、原型链
    - 原型对象：即指的是prototype对象
      - 通过对象直接量创建的对象Object.prototype作为他的原型
      - 通过new创建的对象使用构造函数的prototype对象作为原型
      - 通过Object.create()创建的对象使用第一个参数作为原型，也可以是null

    - 原型链：每个对象都有一个指向它的原型对象（prototype）的内部链接(__proto__ 原型指针)。这个原型对象又有自己的原型，直到某个对象的原型为 null 为止（ Object.prototype.__proto__ ），组成这条链的最后一环。这种一级一级的链结构就称为原型链（prototype chain）。

      ```js
        function Person () {}
        let person1 = new Person()

        Object.prototype.__proto__ // null
        Function.prototype.__proto__ === Object.prototype
        Function.__proto__ === Function.prototype // 特例 唯一一个原型指针指向本身原型对象
        Object.__proto__ === Function.prototype // Object、Date等 属于构造函数
        Person.__proto__ === Function.prototype
        person1.__proto__ === Person.prototype
      ```

    - instanceof 用于判断实例是否是指定构造函数的实例
      例：
      ```js
        let peop = new People3()
        peop instanceof People3 // true
        // 即 peop.__proto__ === People3.prototype

        peop instanceof Object // true  
        // 即 People3.prototype.__proto__ === Object.prototype
      ```

  - #### 5、JavaScript实现继承的几种方式
    ```JavaScript
      // 构造函数
      function People (name) {
        // 构造属性
        this.name = name
        this.sleep = function () {
          console.log(name + ':sleep')
        }
      }
      // 原型
      People.prototype.eat = function (food) {
        console.log(food)
      }

      1、原型链继承
      function People1 () {}
      People1.prototype = new People()
      // 方法重载
      People1.prototype.eat = function (food) {
        console.log(food)
      }

      // 缺点：实例的原型指向同一个构造函数的实例，修改数据是会互相影响

      2、构造继承（使用父级的构造函数来增加子类属性）
      function People2 () {
        People.call(this)
        this.like = 'cooding'
      }

      // 缺点：无法继承原型链

      3、组合继承 （通用继承方式）
      function People3 (name) {
        People.call(this)
      }
      People3.prototype = new People()
      // People3.prototype = People.prototype // 优化1
      // People3.prototype = Object.create(People.prototype) // 优化2
      // 需要修复构造函数指向
      People3.prototype.constructor = People3

      4、实例继承
      function People4 (name) {
        let people = new People()
        people.name = name || 'Tom'
        return people
      }

      // 缺点：实例是父类的实例，不是子类的实例

      5、拷贝继承
      function People5 (name) {
        let people = new People()
        for (let key in people) {
          People5.prototype[key] = people[key]
        }
      }

      // 缺点：1.效率较低，内存占用高
      //      2.无法获取父类不可枚举的方法

      6、class继承
      class People6 extends People {
        constructor(name) {
          super(name)
        }
      }
    ```

  - #### 6、DOM事件基本概念
    - DOM事件级别：
      - DOM0：`elem.onClick(function () {})`
      - DOM2：
        - IE：`elem.attachEvent('click', function () {})`
        - chrome等：`elem.addEventListener('click', function () {}, false)`
      - DOM3：`elem.addEventListener('click', function () {}, false)`

    - DOM事件模型 指事件捕获、事件冒泡
    - 事件流 捕获阶段 -> 目标阶段 -> 冒泡阶段
    - DOM捕获具体流程 window -> document -> html
    - Event对象：
      ```js
        event.preventDefault // 阻止默认事件
        event.stopPropagation // 阻止冒泡

        // 场景：一个按钮a、b两个事件，在a事件中添加该事件即可阻止b事件
        event.stoplmmediatePropagation // 事件优先级

        event.currentTarget // 始终返回绑定该事件的元素
        event.target // 返回触发事件的元素
      ```
    - 自定义事件
      ```js
        var event = new CustomEvent('customEv', options)
        elem.addEventListener('customEv', function (res) {
          /* res = options */
        })
        elem.dispatchEvent(event) // 事件触发
      ```

  - #### 7、事件冒泡和事件捕获 以及 事件委托
      - 事件冒泡：事件开始最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到根节点。
      - 事件捕获：接收事件的顺序为根节点到具体的节点。

      - DOM2级事件处理程序
        - `addEventListener()`和`removeEventListener()`。
        它们接受三个参数：处理的事件名称，事件处理程序，Boolean。true则为在事件捕获阶段处理，false为在事件冒泡阶段处理。

        ```javascript
          function handler () {
            alert('click')
          }
          dom.addEventListener('click', handler, false | true)
        ```

      - 事件委托
        - 利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。例如 ul li点击事件
        ```html
          <ul id="ul">
            <li>111</li>
            <li>222</li>
            <li>333</li>
            <li>444</li>
          </ul>
        ```
        ```javascript
          let ul = document.getElementById('ul')
          ul.onclick = function (e) {
            e = e | window.event
            if (e.target.tagName.toLowerCase() === 'li') {
              console.log(e.target.innerHtml)
            }
          }
        ```
        - 优点
          - 可以大量节省内存占用，减少事件注册。
          - 可以实现当新增子对象时，无需再对其进行事件绑定，对于动态内容部分尤为合适
        - 缺点：事件代理的常用应用应该仅限于上述需求，如果把所有事件都用事件代理，可能会出现事件误判。即本不该被触发的事件被绑定上了事件。

  - #### 8、函数去抖 和 函数节流（[源](https://www.jianshu.com/p/0dbe40b7c1cf)）
    - 函数去抖：当事件触发之后，必须等待某一个时间之后，回调函数才会执行，假若再等待的时间内，事件又触发了则等待时间刷新。
      ```javascript
        var debounce = function(delay, cb) {
          var timer;
          return function() {
            if (timer) clearTimeout(timer);
            timer = setTimeout(function() {
              cb();
            }, delay);
          }
        }
      ```

    - 函数节流：当事件触发之后，按照一个固定时间周期执行。
      ```javascript
        var throttle = function(delay, cb) {
        var startTime = Date.now();
          return function() {
            var currTime = Date.now();
            if (currTime - startTime > delay) {
                cb();
                startTime = currTime;
            } else {
              timer = setTimeout(cb , delay);
            }
          }
        }
      ```
    - demo: window.resize或window.scroll
      - 使用函数去抖后，会在停止resize事件后等待某时间触发回调
      - 使用函数节流，会导致resize事件固定时间周期触发一次事件回调

  - #### 9、错误监控
    - 错误类型
      - 及时运行错误
        - try...catch
        - window.onerror

      - 资源加载错误
        - object.onerror
        - performance.getEntries() 获取已加载的资源
          > 所有img DOM节点扣去已加载的资源

        - Error事件捕获
          ```js
            window.addEventListener('error', function (err) {
              console.log(err)
            }, true)
          ```
    - 上报错误的基本原理
      - 采用ajax通信上报错误
      - 使用image对象上报错误
        ```js
          (new Imgae()).src = 'url'
        ```

  - #### 10、AJAX
    - XMLHttpRequest
      ```js
        let xmlhttp = null
        if (window.XMLHttpRequest) {
          xmlhttp = new XMLHttpRequest()
        } else if (window.ActiveXObject) {
          xmlhttp = new ActiveXObject('Microsoft.XMLHTTP')
        }

        xmlhttp.onreadystatechange = callback
        xmlhttp.open('GET', url, true)
        xmlhttp.send(null)

        function callback (res) {
          if (xmlhttp.readyState == 4) {
            if (xmlhttp.status == 200) {// 200 = OK
              // ...our code here...
            } else {
              alert("Problem retrieving XML data");
            }
          }
        }
      ```
    - fetch
      ```js
        fetch(url, {
          method: 'get',
          headers: { 'X-Token': 'fe9' }
        }).then(res => {
          // Result
        }).catch(err => {
          // Error
        })
      ```

### ES6常用语法
  - 块级作用域 `let/const`
  - 箭头函数 `map(() => {})`
  - 解构函数 `let {a} = {a: 1}`
  - 多行字符串/模板变量  `${a}`
  - 函数默认参数 `function (a = 1, b = false) {}`
  - 扩展运算符 `arr = [...arr, 1]`
  - `proxy` 代理
  - `class`类
  - 模块化 `export`、`import`
  - `Set、Map`
  - `promise`
    - 源于 `jquery.deferred` (<JQ_1.5)、`jquery.deferred().promise()` (>JQ_1.5)
    - Promise异常捕获 （Error、reject）
    - Promise.all、Promise.race
    ```js
      // 必须所有promise实例都加载完成
      promise.all([result1, result3]).then(datas => {
        console.log(datas) // datas 返回一包含两个Promise返回值的数组
      })

      // 只要一个promise实例加载完成 即执行 .then
      promise.race([result1, result3]).then(data => {
        console.log(data) // data 加载成功的实例的返回值
      })
    ```
    - 规范
      - Promise必须包含三种状态 `pedding`、`fulfilled`、`rejected`
      - 状态改变 pedding —> fulfilled 、pedding —> rejected
      - 状态不可逆
      - Promise必须实现`.then()`方法
      - .then方法可接收两个函数作为参数
      - .then方法必须返回一个Promise对象（默认返回自己本身）
      - Promise必须实现.catch()方法，可接收一个函数作为参数

  - generator
    - `yield`: 暂停代码
    - `next()`: 继续执行代码
    ```js
      function* test() {
        yield '1';
        yield '2';
        return 'end';
      }

      const generator = test();

      generator.next() // { value: '1', done: false }
      generator.next() // { value: '2', done: false }
      generator.next() // { value: 'end', done: false }
      generator.next() // { value: undefined, done: true }
    ```
  - `async、await` 是generator的语法糖， babel中是基于promise实现

### CSS
- #### 1、link 与 import 的区别
  - import
    ```css
      @import "./assets/reset.less";
    ```
  - link
    ```html
      <link rel="prefetch" href="/assets/reset.css">
    ```
  - 区别：
    - link可以通过js动态引入
    - link可以定义`RSS`、`Rel（prefetch）`等作用
    - 当解析到link时，页面会同步加载所引的 css，而@import所引用的 css 会等到页面加载完才被加载
- #### 选择器的优先级
  - `!important` > 行内样式 > `#id` > `.class` > `tag` > * > 继承 > 默认

- #### 2、BFC/IFC（内联元素格式化上下文）
  - BFC
    - 概念：块级格式化上下文
    - 原理（渲染规则）：
      - BFC垂直方向边距会发生重叠
      - BFC是一个独立的容器，内外元素不会互相影响
      - BFC容器计算高度时，内部浮动元素会参与计算（清除浮动的原理）
    - 创建BFC
      - float 值不为 `none`
      - position 值不为 `static`、`relative`
      - display 值为 `table`、`table-cell`、`inline-block`
      - overflow 值不为 `visible`

### HTML
  - #### 1、canvas 和 svg 的区别
    - canvas 是画布，通过JavaScript来绘制，可绘制2D、3D图形，依赖分辨率 适合做动画
    - svg 是矢量图形，是xml的一种元素标签，可以通过操作dom元素的方法来控制，不会根据大小尺寸的变化而影响分辨率

  - #### 2、在HTML代码嵌入 SVG 代码
    ```html
      <body>
        <svg xmlns="http://www.w3.org/2000/svg" version="1.1">
          <circle cx="100" cy="100" r="50" stroke="blank" stroke-width="2" fill="2" />
        </svg>
      </body>
    ```

### 浏览器
  - #### 1、浏览器事件流
    - 事件冒泡
      div -> body -> html -> dom
    - 事件捕获
      dom -> html -> body -> div

    - dom的事件流
      - 事件捕获 -> 事件冒泡
      - dom -> html -> body -> div -> body -> html -> dom

  - #### 2、浏览器渲染机制
    ![渲染机制](../assets/image/浏览器渲染机制.png)  

    - 渲染流程
      1. 处理 HTML 并构建 DOM 树。
      2. 处理 CSS 构建 CSSOM 树。
      3. 将 DOM 与 CSSOM 合并成一个渲染树。
      4. 根据渲染树来布局，计算每个节点的位置。
      5. 调用 GPU 绘制，合成图层，显示在屏幕上。

    - 当 HTML 解析到 script 标签时，会暂停构建 DOM，完成后才会从暂停的地方重新开始。也就是说，如果你想首屏渲染的越快，就越不应该在首屏就加载 JS 文件。并且 CSS 也会影响 JS 的执行，只有当解析完样式表才会执行 JS，所以也可以认为这种情况下，CSS 也会暂停构建 DOM。
    - 重排（Reflow 发生在Layout阶段）
      - DOM节点的新增、修改、删除、会导致Reflow，有点也会导致Repaint
      - 页面尺寸改变（resize）
      - DOM元素样式改变（动画、修改样式）
      - 改变页面默认字体

      >  减少Reflow：
      >  1.集中样式管理，修改时使用修改class代替使用修改style
      >  2.使用absolute脱离文档流
      >  3.使用display：none 代替visibility，少改变z-index

    - 重绘（Repaint）
      - DOM 改动
      - CSS 改动

      > 减少Repaint：DOM元素构建完成后一次性插入DOM节点中

  - #### 3、JavaScript引擎的执行机制（Event Loop）
    - 机制：JavaScript是一门单线程语言，执行机制是使用Event Loop ( 事件轮询 )
    - js的事件任务可以分为 宏任务(mocro-task) 和 微任务(micro-task)
      - 宏任务：`script`、`setTimeout`、`setInterval`
      - 微任务：promise的`then`方法、`process.nextTick`
    - 任务执行顺序
      - 首先执行script下的宏任务，遇到setTimeout等宏任务将其放置于宏任务队列中
      - 遇到promise先执行promise方法，然后将then方法放置入微任务队列中
      - 当本轮宏任务执行结束后执行本轮微任务（即then方法）
      - 当微任务执行结束后，本轮event loop执行完毕
      - 下一轮宏任务开始执行（即setTimeout）
      - ...

  - #### 4、跨域
    - 不同域名之间怎么实现数据通信（纯前端）
      - window.open页面通信
        ```javascript
          const childPage = window.open('child.html', 'child')

          childPage.onload = () => {
            childPage.postMessage('hello', location.origin)
          }

          // child.html
          window.onmessage = evt => {
            // evt.data
          }
          //缺点是只能与自己打开的页面完成通讯，应用面相对较窄；但优点是在跨域场景中依然可以使用该方案。
        ```

      - iframe 子页面向父页面发送信息
        ```javascript
          // parent.html
          window.addEventListener('message', function (e) {
            // 通过origin属性判断消息来源地址
            console.log(e.data)  
          }, false)

          // child.html
          var ifr = window.parent  //获取父窗体
          ifr.postMessage('这是传递给a.html的信息', location.origin);
        ```
      - localStorage （跨浏览器无效）
      - cookie （要求domain域名一致，或子域名相同）
      - webSocket (需要后端来维护)
        > 它实现了浏览器与服务器全双工通信，同时允许跨域通讯  

        ```javascript
          // 新建一个WebSocket对象，
          var ws = new WebSocket('ws://127.0.0.1:8080/url');
          // 注意服务器端的协议必须为“ws://”或“wss://”，其中ws开头是普通的websocket连接，wss是安全的websocket连接，类似于https。
          // 连接被打开时调用
          ws.onopen = function () {};

          // 在出现错误时调用，例如在连接断掉时
          ws.onerror = function (e) {};

          // 在连接被关闭时调用
          ws.onclose = function () {};

          ws.onmessage = function(msg) {
            // 在服务器端向客户端发送消息时调用
            // msg.data包含了消息
          };
          // 这里是如何给服务器端发送一些数据
          ws.send('some data');
          // 关闭套接口
          ws.close();
        ```
        例：[微信小程序websocket](./WebSocket/wxWebSocket.js) 封装

      - JSONP跨域通信
        > 原理：主要是利用script标签不受同源策略限制的特性

        ```javascript
        <script type="text/javascript">
          // 创建script标签
          var url = 'http://localhost.com/api?id=1&callback=jsonpCb'
          var _script = document.createElement('script')
          _script.setAttribute('src', url)
          document.getElementsByTagName('head')[0].appendChild(_script)

          // script标签挂载后回调
          function jsonpCb (data) {
            console.log('name:' + data.name)
          }
        </script>
        ```
    - 服务端
      - 设置 CORS: Access-Control-Allow-Origin：*
      - 利用服务端代理

  - #### 5、[存储](./浏览器数据存储)

  - #### 6、垃圾回收机制
    - 原理：垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存。
    - 回收标记：
      - 标记清除：在申明变量的时候被标记（进入环境），函数调用结束又会被标记（离开环境），被标记为离开环境的变量会被收集器检测时回收
        ```js
          function test()  {
            var a = 10 ; //被标记 ，进入环境
            var b = 20 ; //被标记 ，进入环境
          }

          test() //执行完毕 之后 a、b又被标离开环境，等待被回收
        ```
      - 引用计数：跟踪记录每个值被引用的次数。当声明一个变量并将引用类型的值赋给该变量时，则这个值的引用次数就是1。如果同一个值又被赋给另一个变量，则该值的引用次 数加1.相反，如果包含对这个值引用的变量又取得另外一个值，则这个值的引用次数减1.当这个值的引用次数变成0时，则说明没有办法访问这个值了，因此就 可以将其占用的内存空间回收回来。
        ```js
          var a = {}  // {} 引用次数 1
          var b = a  // {} 引用次数 1 + 1
          a = null // {} 引用次数 2 - 1

          // 引用次数为 1，对象 {} 不会被回收
        ```
    - 回收机制执行时间： 周期性定期执行检测（每个浏览器周期时长不一致）

### 框架：vue

  - #### 1、MVC 和 MVVM
    - M（Model） 数据源
    - V（View） 视图
    - C (Controller) 控制器
    - VM（ViewModel） 连接桥（连接M和V，view通过event-bind影响model，model通过data-bind影响view）

    - MVVM的三要素：
      - 响应式：vue监听data变化（数据劫持defineProperty 结合 发布者-订阅者模式）
      - 模板引擎：vue的如何解析模板，指令如何处理
        - 模板本质： 字符串
        - 将 模板 -> render函数（`vm._c() `、`vm._v()`、`vm._s()`等）_ render函数返回vnode
        - v-model、v-for （`vm._l()` 函数）、v-if等指令
      - 视图渲染：vue模板如何渲染成html，及渲染过程（通过`patch`函数初始化渲染及`rerender`）

  - #### 2、数据双向绑定实现原理 [参考](https://segmentfault.com/a/1190000006599500)
    - vue v-model 使用<font color="#d27377">数据劫持(defindeProperty)</font>结合<font color="#d27377">发布者-订阅者模式</font>实现  
    - [实现代码](./vue双向数据绑定.js)
    - 原理图
    ![原理图](../assets/image/数据双向绑定原理图.png)

  - #### 3、vue的实现流程
    - 模板解析成render函数
      - with的用法
      - 用render函数模拟模板中所有的DOM标签和属性
      - 并模板中用到的 data 中的属性，都变成了 JS 变量
      - 模板中的 v-model、v-for、v-on 都变成了 JS 逻辑
      - 最终 render 函数返回 vnode

    - 数据双向绑定（数据劫持defineProperty 结合 发布者-订阅者模式）
      - 劫持监听所有属性

    - DOM初始化渲染，订阅数据变化、绑定更新函数  
      - 初次渲染，执行 updateComponent，执行 `vm._render()`
      - render函数会访问到变量，触发get方法
      - get方法会将变量添加到订阅器中
      - 执行 updateComponent ，会走到 vdom 的 patch 方法 （`patch('app', vm._render())`）
      - patch 将 vnode 渲染成 DOM，初次渲染完成

    - data属性变化，触发rerender更新函数
      - 修改属性，被响应式的 set 监听到
      - set 中触发 updateComponent
      - updateComponent 重新执行 `vm._render()`
      - 生成的 `vnode` 和 `prevVnode` ，通过 patch 进行对比 `patch(prevVnode, vnode)`
      - 重新渲染指定DOM节点到html中

  - #### 4、Virtual Dom
    - 真实DOM操作效率低
    - Virtual Dom优点：使用js来模拟DOM结构，通过js比对DOM前后将发生的变化，并针对具体节点做修改，减少DOM重绘提高性能。
      ```js
        // vnode结构
        {
          tag: 'ul',
          attrs: { id: 'ulList' },
          children: [
            {
              tag: 'li',
              attrs: { className: 'item' },
              children: ['Li1—text']
            },
            {
              tag: 'li',
              attrs: { className: 'item' },
              children: ['Li2—text']
            }
          ]
        }
      ```
    - Virtual Dom常用API （snabbdom等虚拟dom库）
      - snabbdom 使用demo
        ```js
          var snabbdom = window.snabbdom
          var h = snabbdom.h

          var patch = snabbdom.init([
            snabbdom_class,
            snabbdom_props,
            snabbdom_style,
            snabbdom_eventlisteners
          ])

          // 表格属性
          var data = [
            { name: '姓名', age: '年龄', sex: '性别' },
            { name: 'undefind', age: '23', sex: '男' },
            { name: 'null', age: '18', sex: '女' }
          ]

          var app = document.getElementById('app')
          var vnode

          function render (data) {
            var newVnode = h('table', {}, [
              data.map(function(item) {
                var tds = []
                for (var key in item) {
                  if (item.hasOwnProperty(key)) {
                    tds.push(h('td', {}, item[key]))
                  }
                }
                return h('tr', {}, tds)
              })
            ])
            if (!vnode) {
              // 首次渲染
              patch(app, newVnode)
            } else {
              // re-render
              patch(vnode, newVnode)
            }
            vnode = newVnode
          }

          render(data)

          setTimeout(function () {
            data[1].age = 25
            data[2].name = 'test'
            data.push({ name: 'final', age: '20', sex: '女' })
            render(data)
          }, 5000)
        ```
      - h函数：h('<标签名>', {...属性...}, [...子元素...])
      - patch函数： patch(app, vnode) 初次渲染节点、patch(vnode, newVnode)
        - patch(app, vnode)示例
          ```js
            function createElement (vnode) {
              var tag = vnode.tag
              var attrs = vnode.attrs || {}
              var children = vnode.children || []
              if (!tag) return null

              // 创建真实DOM元素
              var elem = document.createElement(tag)
              // 设置属性
              for (var attrName in attrs) {
                if (attrs.hasOwnProperty(attrName)) {
                  elem.setAttribute(attrName, attrs[attrName])
                }
              }

              // 子元素
              children.forEach(function (childVnode) {
                // 递归添加子元素
                elem.appendChild(createElement(childVnode))
              })
              return elem
            }
          ```
        - patch(vnode, newVnode)简单示例
          ```js
            function updateChildren (vnode, newVnode) {
              var children = vnode.children || []
              var newChildren = newVnode.children || []

              children.forEach(function (childVnode, index) {
                if (childVnode.tag === newChildren[index].tag) {
                  // 递归比对
                  updateChildren(childVnode.children)
                } else {
                  // 替换
                  replaceChildren(vnode, newVnode)
                }
              })
            }

            function replaceChildren (vnode, newVnode) {
              let elem = vnode.elem // 真实DOM节点
              let newElem = document.createElement(newVnode)

              // 替换
            }
            // 节点新增和删除
            // 节点重新排序
            // 节点属性、样式、事件绑定
            // 极致压榨性能
            // ...
          ```

    - diff算法：比对差异
      - linux diff：比对两文件之间的差异
      - git diff：比对当前文件修改前修改后的差异

    - Virtual Dom中的diff算法的作用：找出前后两个vdom节点的差异，并仅更新差异部分

### 构建工具
  - #### 1、webpack理解
    - webpack 是模块化项目构建工具，通过Loader转换文件，通过Plugin注入钩子，最后输出由多个模块组合成的文件。符合生产环境部署的前端资源。

    - 与其他构建工具（gulp等）的区别：
      - gulp
        - gulp 是流程化项目构建工具，通过配置一系列的task，定义task处理的事务（例如文件压缩合并、雪碧图、启动server、版本控制等），然后定义执行顺序，来让gulp执行这些task，从而构建项目的整个前端开发流程。
        - gulp工作方式：在一个配置文件中，指明对某些文件进行类似编译，组合，压缩等任务的具体步骤，这个工具之后可以自动替你完成这些任务。
      - webpack
        - 更注重强调模块化开发，把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个浏览器可识别的JavaScript文件。

    - loader 的机制
      - 用来加载处理各种形式的资源，本质上是一个函数，接受文件作为参数，返回转化后的结构。
      - 用于对模块的源代码进行转换。其类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法

      ```js
        rules: [
          {
            test: /\.vue/,
            use: [
              {
                loader: 'vue-loader',
                include: [resolve('src')],
                options: {
                  ...
                }
              }
            ]
          }
        ]
      ```
    - plugin
      - 一个JavaScript函数或者class（ES6语法）。
      - 在它的原型上定义一个apply方法。
      - 指定挂载的webpack事件钩子。
      - 处理webpack内部实例的特定数据。
      - 功能完成后调用webpack提供的回调。

      ```js
        // Compiler对象代表了完整的 webpack 环境配置。
        // 这个对象在启动 webpack 时被一次性建立，并配置好所有可操作的设置，包括 options，loader 和 plugin。
        import { Compiler } from 'webpack'

        // 创建一个新的 compiler 实例
        const compiler = new Compiler()

        compiler.options = {...} // 填充所有必备的 options 选项

        class PluginDemo {
          apply (compiler) {
            compiler.plugin('emit', function(compilation, callback) {
              // 执行一些异步……
              setTimeout(function() {
                console.log('异步运行完成...');
                callback()
              }, 1000)
            })
          }
        }
      ```

    - webpack 常见的事件钩子  

      |钩子|作用|参数|类型|
      |---|---|---|---|
      | after-plugins  | 设置完一组初始化插件之后 | compiler | sync |
      | after-resolvers| 设置完resolvers之后    | compiler | sync |
      | run	           | 在读取记录之前	       | compiler |	async |
      | compile        | 在创建新compilation之前|	compilationParams |	sync             |
      | compilation    | compilation创建完成   |	compilation |	sync|
      | emit           | 在生成资源并输出到目录之前|	compilation |	async|
      | after-emit     | 在生成资源并输出到目录之后|	compilation |	async|
      | done           | 完成编译|	stats |	sync |

    - loader 和 plugin 的区别
      - loader是用来对模块的源代码进行转换，而插件（plugin）目的在于解决 loader 无法实现的其他事。
      - plugin可以在任何阶段调用，能够跨Loader进一步加工Loader的输出，在构建运行期间，触发事件，执行预先注册的回调，使用compilation对象做一些更底层的事情。

    - 常用的loader
      - babel-loader
      - vue-loader
      - url-loader
      - less/sass-loader
      - style-loader
      - css-loader
      - postcss-loader
      - eslint-loader

    - 常用的plugin
      - HtmlPluginWebpack
      - CopyPluginWebpack
      - UglifyJsPlugin
      - BundleAnalyzerPlugin

### 服务端与网络
  - #### 1、[http协议原理](./http协议/http协议.md)
    - http (Hyper Text Transfer Protocol) 超文本传输协议
      - http请求过程
        - DNS解析 -> 建立TCP连接 -> 发送请求头和请求行 -> 发送请求体 -> 等待服务器返回首字节(响应头) -> 接收数据
      - HTTP 标准端口是80 ，而 HTTPS 的标准端口是443
    - http常用状态码

      - 100  Continue   继续，一般在发送post请求时，已发送了http header之后服务端将返回此信息，表示确认，之后发送具体参数信息

      - 200  OK         正常返回信息
      - 201  Created    请求成功并且服务器创建了新的资源
      - 202  Accepted   服务器已接受请求，但尚未处理
      - 206  Partial Content 成功执行了一个部分或Range(范围)请求。206响应中必须包含Content-Range、Date以及ETag或Content-Location首部。

        (重定向)
      - 301  Moved Permanently  请求的网页已永久移动到新位置。
      - 302  Found        临时性重定向。
      - 303  See Other    临时性重定向，且总是使用 GET 请求新的 URI。
      - 304  Not Modified 自从上次请求后，请求的网页未修改过。

        (客户端错误)
      - 400  Bad Request  服务器无法理解请求的格式，客户端不应当尝试再次使用相同的内容发起请求。
      - 401  Unauthorized 请求未授权。
      - 403  Forbidden    禁止访问。
      - 404  Not Found    找不到如何与 URI 相匹配的资源。

        (服务端错误)
      - 500  Internal Server Error  最常见的服务器端错误。
      - 503  Service Unavailable    服务器端暂时无法处理请求（可能是过载或维护）。

      ......

  - #### 2、浏览器攻击（XSS、CSRF）[来源](https://mp.weixin.qq.com/s/sjy-Kul5y0Wj-HJwQn7gBA)
    - XSS 跨站脚本注入攻击
      - 原理：服务端过于信任客户端提交的数据，没有对提交的信息进行过滤
      - 解决：
        1. 输入输出内容检查并过滤
        2. Cookie中设置HttpOnly (设置HttpOnly的Cookie通过js脚本将无法读取到)

    - CSRF 跨站请求伪造(挟持)
      - 要求：
        1. 登录受信任网站A，并在本地生成Cookie。
        2. 在不登出A的情况下，访问危险网站B。B网站拿到A网站的cookie，并发送请求。
      - 解决：
        1. 验证码
        2. 请求验证Referer来源
        3. token验证（jwt）

### 算法
  - #### 1、[排序算法](./算法/sort.js)

  - #### 2、[一维数组构建树结构](./算法/tree.js)

### 数据结构
  - 线性表
    - 数组
      - 概念：数组可以在内存连续存储多个元素的结构，在内存中分配也是连续的，通过下标进行访问
      - 优点：通过下标查询和遍历速度快
      - 缺点：需初始化容量，一经确定不可更改，数组中只能存同一类型的数据，删除和添加速度慢（需移动其他元素）
    - 链表
      - 链表：是一种物理上非连续的、非顺序的存储结构，数据的逻辑顺序通过链表的指针地址来实现的，每个元素都包含两个节点（`存储数据的内存空间` 和 `指向下一节点的指针`），由指针指向可以形成`单链表`、`双向链表`、`循环链表`等
      - 优点：不需要初始化容量，可随意增加删除（只需修改前后两个元素指针指向）
      - 缺点：元素中包含大量指针，占用内存。通过链表查询元素，速度慢

    - 栈
      - 概念：栈属于特殊的线性表，只允许栈顶进行操作，特点：先进后出、后进先出
      - 应用场景：递归功能场景

    - 队列
      - 概念：队列属于特殊的线性表，特点是在一端插入，在另一端取出，先进先出
      - 应用场景：多线程阻塞队列

  - 树与二叉树
  - 散列表
  - 堆
  - 图

### 知识拓展

  - #### 1、node给前端带来了哪些变化（应用场景）
    - 使用node开发后端
    - react、vue等前端框架辅助开发(webpack)
    - 桌面应用、插件等（atom）

  - #### 2、WebView和原生是如何通信
    - 核心原理：需要一门语言实现Native端和H5端之间的双向通信，即JSBridge

    - JavaScript 通知 Native
      - API注入： Native获取JS环境的上下文，并直接挂在对象或方法，使js可以直接调用原生方法
      - webview中的prompt拦截，通过对webview信息冒泡传递拦截
      - webview url scheme调整拦截
    - Native 通知 Javascript
      Native拥有最高权限，可以直接通过 webview api 执行js代码

    - JSBridge的引入
      - 由js环境和Native客户端分别引入JSBridge包
      - 将两个部分的JSBridge包封装成Native SDK，由客户端统一引入，客户端在初始化webview打开页面时，如果页面在白名单中，即在HTML头部注入jsbridge

      ```js
        // H5通过iframe访问schema协议通讯
        function _invoke_native (action, data = {}, callback) {
          let iframe = document.createElement('iframe')
          iframe.style.display = 'none'

          // 拼接协议
          let schema = action + '?f=f'
          for (let key in data) {
            if (data.hasOwnProperty(key)) action += `?${key}=${data[key]}`
          }
          iframe.src = schema + '&callback=' + callbackName

          // 回调
          let callbackName = ''
          if (typeof callback === 'string') {
            callbackName = callback // 外部直接调用全局函数
          } else {
            callbackName = action + '_native_callback' + Date.now() // 避免回调重复
          }
          window[callbackName] = callback

          // 调用协议
          var body = document.body || document.getElementsByTagName('body')[0]
          body.appendChild(iframe)
          // 清除DOM节点
          setTimeout(function(){
            body.removeChild(iframe)
            iframe = null
          })
        }

        export default {
          scan: function (data, callback) {
            _invoke_native('jh://scan', data, callback)  
          }
        }

      ```

  - #### 3、jquery、zepto 和 vue、react的区别
    - vue数据和视图分离，解耦（开放封闭原则）
    - vue以数据驱动视图，只关心数据变化，DOM操作被封装
    - jquery等是操作真实DOM的库

    示例：[todo-list](./jquery和vue的区别)

  - #### 4、设计原则
    - 开放封闭原则（对扩展开放，对修改封闭）

  - #### 5、组件化的理解
    - 组件化/模块化更易于团队的开发
    - 封装后的组件更易于维护，使用则不用关心组件内部逻辑
    - 组件可复用，基础组件可组合使用

  - #### 6、typescript 和 JavaScript 的区别
    - typescript 是静态的强类型语言
      - 增加了静态类型、类（面向对象）、模块、接口和类型注解（变量类别必须在头部使用注解定义才可使用）
      - 静态类型化是一种功能，可以在开发人员编写脚本过程中即可检测错误
      - 可用于开发大型的应用(deno等)

    - JavaScript 是动态的弱类型语言
      - 不支持面向对象语言所具有的继承和重载功能
      - 变量可以在任意地方定义和修改，并且会产生变量提升的问题、优势也是弱势、合作开发容易产出不可预料的错误
      - 没有类的概念，es6中的class仍是使用prototype来实现继承

> 个人总结 欢迎纠正
