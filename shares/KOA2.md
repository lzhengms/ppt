title:KOA2
speaker: 郑美双
url: https://github.com/ksky521/nodePPT
transition: slide3
files: /js/demo.js,/css/demo.css,/js/zoom.js
theme: moon
usemathjax: yes

[slide]
## Koa2 大纲

* Koa2的简介
* Koa2和Koa1的区别
* Koa2的安装
* Koa2应用（Application,context,request,response）
* Koa2的中间件（执行顺序）
* Koa2的常用中间件及作用
* Koa2的example
* admin后台使用Koa2的实现

[slide]
## JSSDK简介
* JSSDK主要是为了实现同一个功能在不同平台的兼容调用。
* 提供接口给外部调用，用户不需要关心平台，只要调用一个接口就能实现不同平台上的同一个功能。
* JSSDK内部实现区分不同平台的sdk调用和细节处理。
-----
> 对我们前端来说，JSSDK内部就是要同时实现web和webview两个平台：
* webview,JSSDK内部要判断并加载bridge，进行h5相关的api调用
* web上，要兼容ie8+等一些主流浏览器，JSSDK规范上面有注明<a href="http://git.sdp.nd/fed/guide/blob/master/SDK.md">JSSDK 规范</a>

[slide]
## analytic-api例子介绍
----
* 主要是实现了web上云图和webview里面的umeng的同一个功能---自定义事件的统计,统一对外提供一个接口给用户调用emit，来实现各端的自定义事件统计功能
* 主要通过navigator.userAgent.indexOf('SmartCanWebView') !== -1判断是否是webview里面打开的
* web云图平台：<a href="http://yuntu.101.com/" class="label-danger">yuntu</a>申请appkey->进行审核->产品
* webview的umeng平台：<a href="http://mobile.umeng.com/apps" class="label-primary">umeng</a>
* umeng的账号密码：fjnd99u@gmail.com     ABC123456
* umeng和云图类似: （1）设置->事件->添加事件；（2）功能使用->自定义事件；查看统计结果


[slide]
## 使用云图SDK上的一些注意点
* 云图和友盟一样，都是今天发的数据，要在明天进行统计，不是实时的统计的
* 统计都是基于会话的，初始化云图sdk的时候，要通知开启会话
* 两种情况才会发起请求统计：
（1）满5条数据，才会发起统计请求
（2）监听页面关闭的时候，调用关闭会话的方法，及时不满5条，也会发起统计请求


[slide]

# demo演示

[slide]

## 开发步骤
<span class="label-danger">实际上就是一个npm包，js组件</span>

* 在<a href="http://sdp.nd/main.html">sdp</a>上创建JS组件
* 从之前的很多组件里面都可以拷贝那些常用配置，例如:karma，.babelrc,.editorconfig,eslintrc等配置文件
* 加入打包目录.tools，使用rollup进行打包。使用es6的写法，最终导出的是cjs规范的文件
* 参照规范<a href="http://git.sdp.nd/fed/guide/blob/master/SDK.md">JSSDK 规范</a>和需求进行梳理，哪些需要开发出接口，直接写在class里面；私有的方法最好也不要用_写在类里面，实际上都会被外部访问到。
* 写example进行测试
* 写单元测试


[slide]
## rollup打包，sdp上发布
----
* rollup是下一代的es6模块的打包工具，通过tree-shacking剔除各模块中没有被应用到的方法，会减小包大小
* vuex也是在使用这个打包<a href="https://github.com/vuejs/vuex/tree/master/build">vuex</a>,还有nd-promise等，建议后面写的组件都可以用这个打包方式
用es6写法，然后使用rollup打包打出cjs格式文件。要配置main和jsnext:main。其中jsnext:main就是给rollup这样的打包工具用的，在生成iife格式的时候，不会报错;
也不用在项目里面的webpack中配置babel-loader里面的include了。
* 在sdp上进行<a href="http://sdp.nd/main.html">sdp</a>发布，发布完之后<a href="http://npm.sdp.nd/browse/keyword/@sdp.nd">组件库</a>查看组件。开发环境版本号会有个0.0.4-dev

[slide]
## 配置main和jsnext:main方式不会导致下面的问题，满足输出是cjs格式，es6打包可以用rollup。
-----
* 纯粹用cjs模式写,在打包成iife格式的时候,module.exports会报错，必须用export default。 <span class="label-primary">因为打包的都是es6模块</span>。
  <img src="/1.jpg" />
* 纯粹用es6写，不打包在浏览器运行会报错还需要配置babel的include。<span class="label-primary">不需要在webpack下modules的babel那边进行配置</span>
  <img src="/2.jpg" />

[slide]
## rollup打包遇到问题

* 组件打包时候报错，组件babel-preset-es2015的bug；现在最新的版本是6.13.2，要回退到版本6.9.0就可以打包成功
<img src="/3.jpg" height="450"/>

[slide]
# 单元测试
