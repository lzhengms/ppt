title: KOA2介绍
speaker: 郑美双
url: https://github.com/ksky521/nodePPT
transition: slide3
files: /js/demo.js,/css/demo.css,/js/zoom.js
theme: moon
usemathjax: yes

[slide]
## Koa2 分享大纲

* Koa2的简介
* Koa2的安装
* Koa2和Koa1的区别
* Koa2应用（Application,context,request,response）
* Koa2的中间件（执行顺序）
* Koa2的常用中间件及作用
* Koa2的example
* admin后台使用Koa2的实现

[slide]
## Koa2的简介

* 由express的团队开发
* nodejs的模块
* 是一个中间件框架，很轻量，没有集成任何中间件，路由都没有内置；而express就集成了健壮的路由，所以相比之下koa更纯粹，轻量。
* 设计理念是通过其他底层中间件提供高级「语法糖」，而不是Koa本身提供. 这大大提高了框架的互操作性和健壮性, 并让中间件开发变得简单有趣。
* 使用generator和异步async编程，是一个更轻量的web开发框架
* generator、async的简单介绍：
 <img src="/koa/1.jpg" />
* 项目地址：https://github.com/koajs/koa

[slide]
## Koa2的安装
* 需要node4.0+</br>
   ● es6语法特性支持，如generator</br>
   ● koa是基于node 4+开发的，使用generator、箭头函数等特性</br>
   ● 内存更省，性能更好</br>
* npm install koa@next
* Koa2支持es7的async/await，所以需要使用babel来转换处理，bable-preset-stage-3支持asyn/await

[slide]
## Koa2和Koa1的区别
---- npm run maw

* (1)
    * Koa1是普通函数写的，所以使用直接使用koa()来初始化
    * Koa2是用es6的class来写的，所以使用的时候，要使用new Koa()来初始化
* (2)
    * Koa1一定要依赖co，中间件只能是generator</br>
    <a href="https://github.com/tj/co/blob/master/index.js">co的核心实现</a></br>
    co自带执行器，并返回promise。相当于es7的async/await了
    * Koa2, co模块已经是可选项了，中间件可以支持三种方式</br>
    ● common function </br>
    ● async function（新增）</br>
    ● generatorFunction </br>
* (3)
    * Koa1支持的中间件参数有一个next
    * Koa2支持的中间件参数有两个ctx,next
* (4)
    * Koa1的中间件中的this指的是单次请求的context
    * Koa2的中间件中的this从ctx中获取
* (5)
    Koa1的中间件迁移到Koa2,可以使用koa-convert转化，koa-convert也是有依赖co模块


[slide]

# Koa应用
---- (Application,context,request,response)
* Koa应用是一个包含了中间件数组的对象，就是new Koa()后的实例
* Koa应用提供了use，listen，callback等方法<a href="http://koajs.com/">api</a>
* koa应用在new Koa()的时候，实际是初始化一些实例上的值，Request和Response的属性和方法委托到Context也是在这一步进行的<br/>
   * koa应用实例的use方法只是把一个个中间件方法push到一个数组中去<br/>
   * koa应用实例的listen方法才是真正启动一个server并监听端口<br/>
   * koa应用实例的listen中启动服务用到的callback方法是初始化中间件，返回一个接收请求时执行的函数，端口接收到请求后都会执行这个函数，并传req和res两个参数给这个函数<br/>
* Koa中的概念Context,Request,Response
   * Context有自身属性，用于框架内部使用；也有Request和Resoponse委托的操作方法，方便用户使用Request和Response
   * Context中有通过delegate中来绑定Request和Response中的属性和方法，method方法是委托方法，getter方法用来委托getter，access方法委托getter+setter
   * 每次接收到一个请求，都会为该请求重新生成一个新的上下文
   * ctx.req是node的原生的request对象<br/>
     ctx.res是node的原生的response对象<br/>
     ctx.reqeust是koa中的request对象<br/>
     ctx.response是koa中的response对象<br/>
     ctx.app是koa的原型对象，应用实例<br/>

[slide]

## Koa中间件（执行顺序）
--- npm run demo2
* 当接收到一个请求时，回去执行所有的中间件
<img src="/koa/2.jpg"/>
* 总结：
  * 请求进入时按照 middleware 的挂载顺序执行, 出来时按照相反的顺序执行
  * next 方法不能执行多次
  * next 方法如果不执行, 其后加载的中间件都无效
  * koa 自动在用户挂载的中间件之后添加了一个 respond 中间件, 为事实上的最终中间件, 用于处理响应数据.
  * 某个 middleware 如果 return 了数据, 其将会作为上一个中间件 next() resolve回调的参数


[slide]
## Koa2的常用中间件及作用
----
* 中间件列表：https://github.com/koajs/koa/wiki
* koa-bodyparser----把post请求的body设置在ctx.request.body上，应用这个中间的顺序要放在koa-router之上
* koa-json------是用来格式化响应的body的json格式
* koa-static-----设置静态目录，当要访问/package.json等文件时，可以去静态目录中查找。跟路由不一样，路由的话是直接/package
* koa-router----设置路由
* koa-convert----用来把0.1的中间件转成0.2的中间件，使其返回promise对象(0.1的中间件是generator,0.2的中间件是async)
* koa-connect-history-api-fallback----重定向所有路由到index.html

[slide]
## Koa2的example
* （1）hello koa
* （2）中间件的执行顺序
* （3）koa-router
* get请求： <br/>
localhost:3000 <br/>
localhost:3000/hello/koa <br/>
* post请求：
用post请求处理URL时，我们会遇到一个问题：post请求通常会发送一个表单，或者JSON，它作为request的body发送，但无论是Node.js提供的原始request对象,<br/>
还是koa提供的request对象，都不提供解析request的body的功能！<br/>
所以，我们又需要引入另一个middleware来解析原始request请求，然后，把解析后的参数，绑定到ctx.request.body中。<br/>
koa-bodyparser就是用来干这个活的。这个koa-bodyparser必须在router之前被注册到app对象上。

[slide]
## admin后台使用Koa2的实现
