# 作为高级前端不能不知道的知识点
### js 执行环境及执行栈
    https://segmentfault.com/a/1190000017890535
### js 事件循环机制
    https://juejin.im/post/59e85eebf265da430d571f89
### js 变量提升
###### 什么是变量提升
       引擎会在解释JavaScript代码之前首先对齐进行编译，编译过程中的一部分工作就是找到所有的声明，并用合适的作用域将他们关联起来，这也正是词法作用域的核心内容
###### 变量提升的具体操作
       变量提升包括: 变量提升，函数提升, 函数字面量提升

       变量提升：将变量的定义提升到父作用域的顶部，默认等于undefined
       函数提升：将变量的定义整体提升,包括赋值
       函数字面量提升: 与变量提升相同

       https://www.cnblogs.com/pqjwyn/p/5365532.html

       ES6 中let const 不存在变量提升的问题， 并且产生了块级作用域。
###### 变量提升的优先级
       https://github.com/yygmind/blog/issues/12

### js 变量的存储空间及垃圾回收机制
    基本数据类型 存储在栈，复杂数据类型存储在堆中， 常量存储在池中。
    tips: 闭包中的变量存储在堆中.

    垃圾回收机制：js有自动的垃圾回收,核心思想就是如何判断内存已经不再使用,主要有两种算法:
        1. 引用计数 -- 不在建议使用
           依据：就是看一个对象是否有指向它的引用
           缺点: 循环引用如果两个对象相互引用，尽管他们已不再使用，但是垃圾回收器不会进行回收，最终可能会导致内存泄露。
        2. 标记清除 --
           依据:无法到达的对象”。从根部（在JS中就是全局对象）出发定时扫描内存中的对象，凡能从根部到达的对象，保留,反之标记为不再使用，稍后进行回收。

### 几种常见的容易引起内容溢出的状况及如何避免
     1. 全局变量
     2. 递归未结束条件
     3. 闭包
     4. 脱离dom的引用
     https://github.com/yygmind/blog/issues/16
     https://www.w3cschool.cn/chromedevtools/8ko91oeg.html

     如何使用chrome 分析内存泄漏问题
     https://blog.csdn.net/c11073138/article/details/84700482


### js 浅拷贝和深拷贝的实现
 数组浅拷贝：splice(0), concat, ...(扩展运算符)
 对象浅拷贝: Object.assign, ...(扩展运算符)
   说明 1.Object.assign 会跳过那些值为null 或 undefined 的源对象

          let a = { b: {c:4} , d: { e: {f:1}} }
          let g = Object.assign({},a)
          let h = JSON.parse(JSON.stringify(a));
          console.log(g.d)      // { e: { f: 1 } }

          g.d.e = 32
          console.log('g.d.e set to 32.') // g.d.e set to 32.
          console.log(g) // { b: { c: 4 }, d: { e: 32 } }
          console.log(a) // { b: { c: 4 }, d: { e: 32 } }
          console.log(h) // { b: { c: 4 }, d: { e: { f: 1 } } }

          h.d.e = 54
          console.log('h.d.e set to 54.') // h.d.e set to 54.
          console.log(g) // { b: { c: 4 }, d: { e: 32 } }
          console.log(a) // { b: { c: 4 }, d: { e: 32 } }
          console.log(h) // { b: { c: 4 }, d: { e: 54 } }


数组、对象深拷贝:
    1. JSON.parse(JSON.Stringfy(obj))
    2. 自己开发deepCopy方法

    function typeOf (obj) {
      const toString = Object.prototype.toString
      const map = {
        '[object Boolean]': 'boolean',
        '[object Number]': 'number',
        '[object String]': 'string',
        '[object Function]': 'function',
        '[object Array]': 'array',
        '[object Date]': 'date',
        '[object RegExp]': 'regExp',
        '[object Undefined]': 'undefined',
        '[object Null]': 'null',
        '[object Object]': 'object'，
        '[object Symbol]': 'symbol'
      }
      return map[toString.call(obj)]
    }

    function isArray (object) {
      return typeOf(object) === 'array'
    }

    function isObject (object) {
      return typeOf(object) === 'object'
    }

    function deepCopy (data) {
      let o
      if (isArray(data)) {
        o = []
      } else if (isObject(data)) {
        o = {}
      } else {
        return data
      }
      if (isArray(data)) {
        for (let i = 0; i < data.length; i++) {
          o.push(deepCopy(data[i]))
        }
      } else if (isObject(data)) {
        for (let i in data) {
          o[i] = deepCopy(data[i])
        }
      }
      return o
     }
### js中 null 与 undefined 的区别
##### null 与 undefined 基本是同义的，只有细微的区别。
###### null表示"没有对象"，即该处不应该有值。典型用法是：
      1） 作为函数的参数，表示该函数的参数不是对象。
      2） 作为对象原型链的终点。
###### undefined 表示"缺少值"，就是此处应该有一个值，但是还没有定义。典型用法是：
      1）变量被声明了，但没有赋值时，就等于undefined。
      2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。
      3）对象没有赋值的属性，该属性的值为undefined。
      4）函数没有返回值时，默认返回undefined


### js解决跨域问题
##### 同源策略
      同源策略是浏览器的安全基石,同源策略是指 请求资源的协议、域名、端口号和当前网址的完全一样。
##### 常用的方法及工作原理

###### 常用的方法
###### CORS、jsonp、iframe、script、代理、反代理
       CORS: 定义: 跨域资源共享（ CORS ）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行
             原理: 跨域资源共享标准新增了一组 HTTP 首部字段，允许服务器声明哪些源站通过浏览器有权限访问哪些资源
                  a)针对简单请求(get, post, head)直接请求(request 中带有origin字段)，response中带有access-control-allow-origin，根据
                    Origin 和 Access-Control-Allow-Origin 就能完成最简单的访问控制是否支持
                  b)需预检的请求(delete、put、connect、options、trace、patch)首先使用options方法发起一个预检请求到服务器，以获知服务器是否
                    允许该实际请求
                  说明: 一般跨域的请求是不允许携带身份凭证的,如果需要，服务器端的响应要携带 Access-Control-Allow-Credentials: true

      jsonp：定义: 通过请求script标签实现跨域请求，然后在服务端输出JSON数据并执行回调函数
             原理：1.首先在客户端注册一个callback方法，放到window对象上，然后把callback的名字（callbackFunction）传给服务器。
                  2.服务器先生成 JOSN 数据。
                  3.将 JOSN 数据直接以入参的方式，放置到 function 中，后生成了一段 js 语法的文档（如callbackFunction(JOSN),返回给客户端。
                  4.客户端浏览器，将返回的JS标签插入DOM，解析script标签后，会执行callbackFunction(JOSN)
     正向代理：定义： 客户端要向代理服务器发送一个请求，并指定目标服务器，代理服务器将目标服务器返回的数据转交给客户端
             原理：

     反向代理：定义：客户端将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返回给客户端，
                  此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器IP地址-- 客户端不需要做什么
             原理：

     反向代理的好处: https://juejin.im/post/5bacbd395188255c8d0fd4b2


###### 作用域链
######## 定义：作用域链：当访问一个变量时，解释器会首先在当前作用域查找标示符，如果没有找到，就去父作用域找，直到找到该变量的标示符或者不在父作用域中
######## 提示：作用域链和原型继承查找时的区别：
              如果去查找一个普通对象的属性，但在当前对象和其原型中都找不到时，会返回undefined；但查找的属性在作用域链中不存在的话就会抛出ReferenceError。
