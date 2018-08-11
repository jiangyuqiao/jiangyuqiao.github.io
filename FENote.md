# 面试笔试问题总结
## JavaScript

### - DOM操作
```js
createDocumentFragment()    //创建一个DOM片段
createElement()   //创建一个具体的元素
createTextNode()   //创建一个文本节点

appendChild()
removeChild()
replaceChild()
insertBefore() //在已有的子节点前插入一个新的子节点

getElementsByClassName()
getElementsByTagName()    //通过标签名称
getElementsByName()    //通过元素的Name属性的值(IE容错能力较强，会得到一个数组，其中包括id等于name值的)
getElementById()    //通过元素Id，唯一性
```

### - SetTimeout(*function*, 0)
javascript是单线程执行的，也就是无法同时执行多段代码，当某一段代码正在执行的时候，所有后续的任务都必须等待，形成一个队列，一旦当前任务执行完毕，再从队列中取出下一个任务。这也常被称为 “阻塞式执行”。所以一次鼠标点击，或是计时器到达时间点，或是 Ajax 请求完成触发了回调函数，这些事件处理程序或回调函数都不会立即运行，而是立即排队，一旦线程有空闲就执行。假如当前 JavaScript 进程正在执行一段很耗时的代码，此时发生了一次鼠标点击，那么事件处理程序就被阻塞，用户也无法立即看到反馈，事件处理程序会被放入任务队列，直到前面的代码结束以后才会开始执行。如果代码中设定了一个 setTimeout，那么浏览器便会在合适的时间，将代码插入任务队列，如果这个时间设为 0，就代表立即插入队列，但不是立即执行，仍然要等待前面代码执行完毕。所以setTimeout 并不能保证执行的时间，是否及时执行取决于 JavaScript 线程是拥挤还是空闲。

### - ES6新特性
- 模板字符串 & 多行字符串
- 解构赋值
- 箭头函数
- 块级作用域（let）
- [let与const](https://www.tuicool.com/articles/ueM3Mvr)
- class定义类
- 模块化
- Promise
- Map与Set

### - Promise
Promise与EventListener的区别
- promise 只能成功或失败一次，而不能成功或失败两次，也不能从成功转为失败或从失败转为成功。
- 如果 promise 已成功或失败，且您之后添加了成功/失败回调，则将会调用正确的回调，即使事件发生在先。

- Promise 构造函数包含一个参数和一个带有 resolve（解析）和 reject（拒绝）两个参数的回调
- 若在then()中返回一个值，则以该值调用下一个then()；若返回一个Promise，下一个then()则等待其产生结果后调用。因此可以实现链式调用，避免回调嵌套
#### Ref.

### - JavaScript中this的指向
- 没有任何修饰的函数调用（独立函数调用）：默认绑定规则。this指向全局，严格模式下指向undefined
- 隐式绑定：调用位置存在上下文时，this指向上一级的调用位置
- 显式绑定：`bind()`、`apply()`、`call()`
- new绑定：
  1. 构造一个新的对象
  2. 这个新对象会被执行`[[prototype]]`链接
  3. 函数调用的this会绑定到这个新对象上
  4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新的对象
- 箭头函数中的this：封闭执行上下文的this值（根据定义时所在的执行上下文决定） 

### - JavaScript模块化
#### CommonJS
- CommonJS在服务器端广泛使用（Node、Webpack）
- 通过`module.exports`导出对外变量与接口
- `exports` 只是对 `module.exports` 的一个全局引用，如果把`exports`指向其他对象，就打破了两者之间的引用关系
- 通过`require()`来引入模块
- 模块加载是同步的，也就是说，只有加载完成，才能执行后面的操作
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存
- 模块加载的顺序，按照其在代码中出现的顺序

```javascript
// example.js
var x = 5;
var addX = function (value) {
  return value + x;
};
module.exports.x = x;
module.exports.addX = addX;
```
```javascript
// 加载模块
var example = require('./example.js');

console.log(example.x); // 5
console.log(example.addX(1)); // 6
```

#### AMD (Asynchronous Module Definition)
- 异步加载模块，适合浏览器端
- 使用`define()`定义模块；使用`require([module], callback)`加载模块

```HTML
<!-- 加载require.js并加载主模块 -->
<script src="js/require.js" data-main="js/main"></script>
```
```javascript
// main.js

require(['jquery', 'underscore', 'backbone'], function ($, _, Backbone){
　　　　// some code here
　　});

// 或者
require.config({
　　　　paths: {
　　　　　　"jquery": "jquery.min",
　　　　　　"underscore": "underscore.min",
　　　　　　"backbone": "backbone.min"
　　　　},
       // 加载非AMD规范模块
       shim: {
　　　　　　'underscore':{
　　　　　　　　exports: '_'
　　　　　　},
　　　　　　'backbone': {
　　　　　　　　deps: ['underscore', 'jquery'],
　　　　　　　　exports: 'Backbone'
　　　　　　}
　　　　}
　　});
```

```javascript
// 定义模块
define(id?: String, dependencies?: String[], factory: Function|Object);

// 如果没有指定 dependencies，那么默认为
define(function(require, exports, module) {})

// math.js
define(['dependenceModule'], function(dependenceModule) {
  var add = function(x, y) {
    return x + y;
  }

  return  {
    add: add
  };
})
```

#### CMD (Common Module Definition)
- 来自SeaJS，对requireJS与commonJS风格兼容并包

#### UMD (Universal Module Definition)
- 兼容commonJS和AMD

```javascript
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery', 'underscore'], factory);
    } else if (typeof exports === 'object') {
        // Node, CommonJS之类的
        module.exports = factory(require('jquery'), require('underscore'));
    } else {
        // 浏览器全局变量(root 即 window)
        root.returnExports = factory(root.jQuery, root._);
    }
}(this, function ($, _) {
    //    方法
    function a(){};    //    私有方法，因为它没被返回 (见下面)
    function b(){};    //    公共方法，因为被返回了
    function c(){};    //    公共方法，因为被返回了
 
    //    暴露公共方法
    return {
        b: b,
        c: c
    }
}));
```

#### ES6
- 使用`export`导出

```javascript
//导出变量
export var color = "red";
export let name = "cz";
export const age = 25;

//导出函数
export function add(num1,num2){
    return num1+num2;
}

//导出类
export class Rectangle { }

//导出对象，即导出引用
export {add}

// 重命名模块
export {add as sum}

// default关键字
export default function(num1, num2) {
    return num1 + num2;
}
export { add as default };
```

- 使用`import`导入

```javascript
import { identifier1,identifier2 } from "./example.js"
import { sum as add } from './example.js'
// example.sum(1,2);
import * as example from './example.js'

// 导入default定义的模块
import sum from "./example.js";

// 导入默认值和其他非默认模块
import sum,{color} from "./example.js"
```

#### Ref.
- [JavaScript模块化历程](http://web.jobbole.com/83761/)
- [CommonJS规范](http://javascript.ruanyifeng.com/nodejs/module.html)
- [Javascript模块化编程（三）：require.js的用法](http://www.ruanyifeng.com/blog/2012/11/require_js.html)
- [CMD模块定义规范](https://github.com/seajs/seajs/issues/242)
- [javascript模块化之CommonJS、AMD、CMD、UMD、ES6](https://blog.csdn.net/Real_Bird/article/details/54869066)

### - 逻辑或和逻辑与的返回结果

### - 防抖与节流
- 防抖（debounce）：当持续触发事件时，合并这些事件，在一定时间没有触发该事件后再执行事件处理程序
- 节流（throttle）：当持续触发事件时，合并一定时间内的事件，保证每隔一定时间触发一次事件处理程序

### - 引起内存泄漏的操作
- 意外的全局变量（在函数中定义了全局变量或使用了this）
- 被遗忘的计时器回调
- 对DOM的引用（当DOM从文档中移除后）
- 闭包

## HTML

### - The types of input
https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input

### - JS与CSS加载标签在HTML中的位置
- 若放在`<head>`中，则必须等到JS代码下载、解析和执行完成后才开始解析`<body>`中的内容（阻塞文档解析）
- `defer`: 异步加载脚本，在文档完成解析后，`DOMContentLoaded`事件触发前执行
- `async`: 脚本下载完成后即执行，无法控制加载顺序
- [defer和async的区别](https://segmentfault.com/q/1010000000640869)
- CSS文件加载将阻塞渲染树的构建，但若将CSS文件放在文件底部将会引起闪屏

## CSS

### - pseudo-class & pseudo-element
+ :link
+ :visited
+ :active
+ :hover
+ :focus
+ :first-child
+ :lang(*language*)

- ::first-letter
- ::first-line
- ::before
- ::after

### - Flexbox
**容器属性**
```CSS
display: flex; 
flex-direction: row, row-reverse, column, column-reverse; /* 项目排列方向 */ 
flex-wrap: nowrap, wrap, wrap-reverse; /* 是否换行 */ 
/* 以上两个属性可合为 */ flex-flow: row wrap;
justify-content: flex-start, flex-end, center, space-between, space-around; /* 横向对齐方式 */
align-items:  flex-start, flex-end, center, baseline, stretch; /* 一行高度不同的项目的纵向对齐方式: stretch即为等高分栏 */
align-content: flex-start, flex-end, center, space-between, space-around, stretch;/* 多行放置项目时纵向对齐方式 */
```
**项目属性**
```CSS
order: 1; /* 项目排列次序，数值作用类似z-index */
align-self: flex-start, flex-end, center, baseline, stretch; /* 属性值同align-items，单个项目的纵向对齐方式 */
flex-grow: 1; /* 项目宽度相对单位，决定额外空间的分配比例 */
flex-shrink: 1; /* 当项目宽度和大于容器宽度且容器不换行时项目收窄的比例 */
flex-basis: 400px; /* 项目的基准宽度 */
/* 以上三个属性可合为 */ flex: 0 1 auto; /* 浏览器默认值 */ 
```

### - Blocking Formatting Context
Formatting context是页面中的一块渲染区域，并且有一套渲染规则，决定了其子元素将如何定位以及和其他元素的关系和相互作用。常见的 Formatting context 有 Block fomatting context 和 Inline formatting context。


**BFC布局规则：**
- 内部的Box会在垂直方向一个接一个放置
- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box之间会产生margin collapsing (外边距重叠/合并)
- 每个元素的margin box的左边，与包含块border box的左边相接触（对于从左往右的格式化，否则相反）。即使存在浮动也是如此。
- BFC的区域不会与float box重叠
- BFC是页面上的一个隔离的独立容器，容器里的子元素不会影响到外面的元素。
- 计算BFC的高度时，浮动元素也参与计算


**创建BFC的方式：**
- 根元素（body）或包含根元素的元素
- 浮动元素（`float`不为`none`）
- 绝对定位元素（`position`为`absolute`或`fixed`）
- 行内块元素（`display`为`inline-block`）
- `overflow`不为`visible`的块元素
- `display`为`table-cell`, `table-caption`, `flex`, `inline-flex`...

## Algorithm

### - 完全二叉树以及树的度
在一棵二叉树中，除最后一层外，若其余层都是满的，并且最后一层或者是满的，或者是在右边缺少连续若干节点，则此二叉树为完全二叉树（Complete Binary Tree）。每一层上的节点数都是最大节点数的二叉树，称为满二叉树（Full Binary Tree）。

度数是指该结点所含子树的个数。

### - 位运算hacks
``` c
x = x >> 2; // 除以2
x & 0x1 == 1; // 是否为奇数
// TODO: 判断有多少位为1
```



## OS, Network, etc.

### - 进程、线程间通信
#### 进程间通信
信号、管道、消息队列、共享内存、Socket、RPC；同步机制（信号量、互斥锁、条件变量等）
#### 线程间通信
处理线程间的同步与互斥：锁、信号量


### - Event Loop
- **MacroTasks**: script(整体代码), setTimeout, setInterval, setImmediate, I/O, UI rendering
- **MicroTasks**: process.nextTick, Promises, Object.observe(废弃), MutationObserver
#### 浏览器
浏览器会不断从MacroTask队列中按顺序取MacroTask执行，每执行完一个MacroTask都会检查MicroTask队列是否为空（执行完一个MacroTask的具体标志是函数执行栈为空），如果不为空则会一次性执行完所有MicroTask。然后再进入下一个循环去MacroTask队列中取下一个MacroTask执行，以此类推。
#### Node.js
1. timers：执行setTimeout() 和 setInterval()中到期的callback。
2. I/O callbacks：上一轮循环中有少数的I/Ocallback会被延迟到这一轮的这一阶段执行
3. idle, prepare：队列的移动，仅内部使用
4. poll：最为重要的阶段，执行I/O callback，在适当的条件下会 阻塞在这个阶段
5. check：执行setImmediate的callback
6. close callbacks：执行close事件的callback，例如socket.on(“close”,func)


### - HTTP版本演化
#### HTTP/1.0
- 新增`HEAD`、`POST`方法
- Persistent Connection: 支持长连接，但默认还是使用短连接（`Connection: Keep-Alive`）
- 支持缓存机制（`Pragma`、`Expires`）
- 增加Host域指定主机名
- 增加`Content-Range`域，允许只请求资源的某个部分（实现断点续传）
#### HTTP/1.1
- 新增`OPTIONS`、`PUT`、`DELETE`、`TRACE`、`CONNECT`方法
- 默认长连接（除非特别声明 `Connection: close`）
- 请求管道化（pipelining）：避免客户端队首阻塞（head of line blocking，下一个请求必须在前一个请求响应到达后发送）
- 新增`Cache-Control`域
#### HTTP/2.0
- 多路复用，新增二进制分帧层，消息分帧后可以乱序发送，然后再根据每个帧头部的流标识符重新组装（**一个连接上可以同时有多个数据流**）
- 头部压缩：使用HPACK压缩算法，同时通信双方各维护一份header fields表，避免重复头信息传输
- 服务器推送
#### 参考
- [HTTP/2 简介 | Web | Google Developers](https://developers.google.com/web/fundamentals/performance/http2/)
- [HTTP1.0 HTTP1.1 HTTP2.0 主要特性对比 - 前端碎碎念 - SegmentFault 思否](https://segmentfault.com/a/1190000013028798)
- [面试时如何优雅的谈论HTTP／1.0／1.1／2.0 - 简书](https://www.jianshu.com/p/52d86558ca57)

### - `GET`、`POST`以及`PUT`的区别
- https://www.zhihu.com/question/28586791/answer/145424285
- RFC7231里定义了HTTP方法的几个性质：
    1. 安全（Safe）：如果一个方法的语义在本质上是「只读」的，那么这个方法就是安全的。客户端向服务端的资源发起的请求如果使用了是安全的方法，就不应该引起服务端任何的状态变化，因此也是无害的。 此RFC定义，GET, HEAD, OPTIONS 和 TRACE 这几个方法是安全的。
    2. 幂等（Idempotent）：指同一个请求方法执行多次和仅执行一次的效果完全相同。按照RFC规范，PUT，DELETE和安全方法都是幂等的。POST语义不是幂等的，这也是浏览器在后退/刷新时遇到POST会给用户提示的原因：重复请求可能会带来意想不到的后果。
    3. 可缓存性（Cacheable）：顾名思义就是一个方法是否可以被缓存，此RFC里GET，HEAD和某些情况下的POST都是可缓存的，但是绝大多数的浏览器的实现里仅仅支持GET和HEAD。
- [RFC2616](https://tools.ietf.org/html/rfc2616#section-9.6)：
    + 「The PUT method requests that the enclosed entity be stored under the supplied Request-URI. If the Request-URI refers to an **already existing** resource, the enclosed entity SHOULD be considered as a **modified version of the one residing on the origin server**. If the Request-URI does not point to an existing resource, and that URI is capable of being defined as a new resource by the requesting user agent, the origin server can create the resource with that URI.」
- 因此`GET`方法是安全、幂等、可缓存的，`POST`不安全，不幂等，（大部分实现）不可缓存，`PUT`不安全、幂等、不可缓存。


### - HTTP状态码
- __1**__ 信息，服务器收到请求，需要请求者继续执行操作
- __2**__ 成功，操作被成功接收并处理
- __3**__ 重定向，需要进一步的操作以完成请求
- __4**__ 客户端错误，请求包含语法错误或无法完成请求
- __5**__ 服务器错误，服务器在处理请求的过程中发生了错误

[HTTP状态码详解](http://tool.oschina.net/commons?type=5)
[喵星人教你记 HTTP 状态码](http://blog.jobbole.com/88450/)


状态码 | 英文 | 描述 
:---:|:---:|:---
100 | Continue |
101 | Switching Protocols |
||
200 | OK |
201 | Created |
202 | Accepted |
203 | Non-Authoritative Information |
204 | No Content
205 | Reset Content
206 | Partial Content
207 | Multi-Status
||
300 | Multiple Choices
301 | Moved Permanently
302 | Found 
303 | See Other
__304__ | Not Modified
305 | Use Proxy
306 | _Unused_
307 | Temporary Redirect
||
400 | Bad Request
401 | Unauthorized
402 | Payment Required
403 | Forbidden
404 | Not Found
405 | Method Not Allowed
406 | Not Acceptable
407 | Proxy Authentication Required
408 | Request Time-out
409 | Conflict
410 | Gone
411 | Length Required
412 | Precondition Failed
413 | Request Entity Too Large
414 | Request-URI Too Large
415 | Unsupported Media Type
416 | Requested range not satisfiable
417 | Expectation Failed
418 | I'm a teapot
||
500 | Internal Server Error
501 | Not Implemented
502 | Bad Gateway
503 | Service Unavailable
504 | Gateway Time-out
505 | HTTP Version not supported

### - 浏览器缓存机制
- Expires(HTTP/1.0)
- Cache-Control: (HTTP/1.1) public, private, no-store, no-cache (*do-not-serve-from-cache-without-revalidation*), max-age
- Pragma: no-cache
+ Last-Modified / If-Modified-Since
+ ETag (Entity Tag) / If-None-Match (HTTP/1.1)

- [Web 缓存](http://www.yangzicong.com/article/12)
- [彻底理解浏览器缓存机制](https://www.cnblogs.com/shixiaomiao1122/p/7591556.html)

### - 跨域问题
#### CORS（Cross-Origin Resource Sharing，跨源资源共享）
- 非简单请求使用 `OPTIONS` 进行预检
- 请求Header：`Origin`, `Access-Control-Request-Method`, `Access-Control-Request-Headers`
- 响应Headers：`Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Expose-Headers`, `Access-Control-Allow-Credentials`
- 验证失败返回 `403 Forbidden`

#### JSONP（JSON with Padding）
- 兼容性更好，但只支持HTTP GET请求

#### 其他跨域方法
- 修改document.domain
- 使用HTML5的window.postMessage

#### CSRF（Cross-Site Request Forgery，跨站请求伪造）
- 恶意网站利用用户在合法网站获得的Cookie，使用支持跨域的方法（a、img等标签，表单提交）对合法网站发出恶意请求
- 对策：Referer头字段验证、表单Token

#### XSS（Cross Site Script，跨站脚本）
- 恶意攻击者利用网站没有对用户提交数据进行转义处理或者过滤不足的缺点，进而添加一些代码，嵌入到web页面中去。使别的用户访问都会执行相应的嵌入代码。
- 对策：cookie标记为http only（使document.cookie无法获取cookie），对数据进行转义和过滤

#### Ref.
- [CORS——跨域请求那些事儿](https://yq.aliyun.com/articles/69313)
- [浅谈js跨域问题](https://segmentfault.com/a/1190000003784372)
- [CSRF攻击与防御](https://blog.csdn.net/stpeace/article/details/53512283)
- [XSS跨站脚本攻击](https://www.cnblogs.com/phpstudy2015-6/p/6767032.html)

### WebSocket握手
Request
```
GET / HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Host: example.com
Origin: http://example.com
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
Sec-WebSocket-Version: 13
```

Response
```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Location: ws://example.com/
```
- `Sec-WebSocket-Key`是浏览器随机生成的的字符串，服务器端会把`Sec-WebSocket-Key`加上一个特殊字符串`258EAFA5-E914-47DA-95CA-C5AB0DC85B11`后计算SHA-1摘要，之后进行BASE-64编码，将结果做为`Sec-WebSocket-Accept`头的值，返回给客户端。如此操作，可以尽量避免普通HTTP请求被误认为Websocket协议。
- `Sec-WebSocket-Version` 表示支持的Websocket版本。RFC6455要求使用的版本是13，之前草案的版本均应当弃用。

### WebSocket的替代方案
#### 基于 Adobe Flash 实现的 WebSocket（[web-socket-js](https://github.com/gimite/web-socket-js)）
#### 基于长轮询（long-polling）的 XHR
- 服务器端会阻塞请求直到有数据传递或超时才返回。
- 客户端 JavaScript 响应处理函数会在处理完服务器返回的信息后，再次发出请求，重新建立连接。
- 当客户端处理接收的数据、重新建立连接时，服务器端可能有新的数据到达；这些信息会被服务器端保存直到客户端重新建立连接，客户端会一次把当前服务器端所有的信息取回。
#### 基于 multipart 编码发送 XHR

#### Ref.
- [Comet：基于 HTTP 长连接的“服务器推”技术](https://www.ibm.com/developerworks/cn/web/wa-lo-comet/)
- [Comet (programming) - Wikipedia](https://en.wikipedia.org/wiki/Comet_(programming))
- [实时Web的发展与实践](http://www.infoq.com/cn/articles/JavaScript-Web/)
- [使用xhr-multipart方式实现comet](https://cnodejs.org/topic/4f16442ccae1f4aa270010af)

### 常用正则表达式
```
// 匹配.js而不匹配.min.js
(?<!\.min)(\.js)$
// http://blog.sina.com.cn/s/blog_12e4623a90101cqy0.html （（正向）零宽断言 & 负向零宽断言）

^(?!.+\.min.js$).+\.js$    // https://www.zhihu.com/question/55199619/answer/143272684

// 邮箱
^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$

// 千位分隔符
function commafy(num) {
      return num && num
          .toString()
          .replace(/(\d)(?=(\d{3})+\.)/g, function($0, $1) {
              return $1 + ",";
          });
}

// 网址



```

### Git中fetch与pull的区别
- fetch将远程分支的最新内容拉取到本地，并将`FETCH_HEAD`指向此分支
- pull的作用相当于fetch和merge
  ```bash
  git fetch origin master
  git merge FETCH_HEAD
  ```

### - Cookie & Session