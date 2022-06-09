> **CommonJs与ES6中的模块引用有什么区别**

CommonJs是一种模块规范，最初被用于NodeJs，成为NodeJs的模块规范，运行在浏览器端的JavaScript由于也缺少类似的规范，在ES6出来之前，前端也实现了一套一样的模块规范（比如AMD），用于对前端模块进行管理。
自ES6起，引用一套新的ES6 module规范，在语言标准的层面上实现了模块管理，而且实现的相当简单。但目前浏览器对ES6 module的兼容性还不太好，我们平时在webpack里使用的export和import，经过Bable最终会被`转换成CommonJs规范`，在使用上的差别主要有：

- CommonJs模块输出的是一个值的拷贝，ES6输出的是一个值的引用。
- CommonJs模块是运行时加载，ES6模块是编译时输出接口。
- CommonJs是单个值输出，ES6模块可以导出多个。
- CommonJs是动态语法可以写在判断里，ES6 模块是静态语法必须写在顶层。
- CommonJs的this是当前模块，ES6模块的this是undefined。


> **浏览器缓存**

浏览器缓存有`四个方面`，按照获取资源时的优先级依次排列如下：

- Memory Cache
- Service Worker Cache
- HTTP Cache
- Push Cache

缓存又分`强缓存`和`协商缓存`，优先级较高的是强缓存，只有命中强缓存失败的时候，才会采取协商缓存。

- 实现强缓存：过去一直用expires，当服务器返回响应时，在Response Headers中将过期时间写入expires字段，现在一般使用Cache-Control，两者同时出现使用Cache-Control
- 协商缓存： Last-Modified是一个时间戳，如果启用协商缓存，会在首次请求时随着Response Headers返回：每次请求去判断这个时间戳是否发生变化，来决定是返回304读取缓存还是返回最新的数据。


> **console编写代码，统计淘宝首页用了多少种html标签，主要是多少种**

```js
new Set([...document.getElementsByTagName('*')].map(item => item.nodeName))
```

> **升级：并打印出淘宝首页中出现次数前五的html标签**

```js
let tags = [...document.getElementsByTagName('*')].map(item => item.tagName).reduce(( res,item) => {
    res[item] = (res[item] || 0) + 1
    return res
}, {});
let sortList = Object.entries(tags).sort((a, b) => {
    return b[1] - a[1]
})
console.table(sortList.splice(0, 5).map(item => ({'标签名': item[0], '出现次数': item[1]})))
```


> **理解vue中的$nextTick**

浏览器进程分：Browser进程、第三方插件进程、和GPU进程

GPU进程是多线程的，包括：
GUI渲染线程、JS引擎线程、事件触发线程（和EventLoop密切相关）、定时触发器线程、异步HTTP请求线程

其中： GUI渲染线程与JS引擎线程是互斥的，为了防止DOM渲染的不一致性。

与nextTick息息相关的是JS引擎线程和事件触发线程。


事件循环机制：






> Js的数据类型

js数据类型，原本6种，ES6新增了symbol，ES10会新增BigInt

原6种：能备typeof检查出对应类型的数据类型：
undefined、String、Number、Boolean、BigInt、Symbol

另两种都会备typeof识别成Object的【null，Object】
其中Object有可以细分为 set、map、weakset、weakmap、Array、Date、Object


> 为什么JS的数据类型范围是（-(253 -1) 到 253 -1）

js的Number类型本质是一个64位浮点数（8字节）。
但是范围却是 （-(253 -1) 到 253 -1）。
因为js采用的 是IEEE754标准的64位双精度浮点数。
最高位是符号位，接着11位是指数，剩下的52位为有效数字。 因此，JavaScript提供的有效数字最长为53个二进制位（64位浮点的后52位+有效数字第一位的1）




> JS性能优化  34 *10 = 340 = 6h

1、请求资源使用的网络
2、内存管理，基本算法， V8引擎

内存管理：

内存泄露




垃圾回收：

js的内存管理是自动的


什么是垃圾，在后续的程序和代码中  不再被使用，

当前引用或者作用域链的引用，就算【可达对象】
当前的全局对象即为【根】  引用数字变化

function objGroup (obj1, obj2) {
    obj1.next = obj2
    obj2.prev = obj2
    return {
        o1: obj1,
        o2: obj2
    }
}


let obj = objGroup({name: 'obj1'}， {name: 'obj2'})


console.log(obj)


GC算法--  垃圾回收机制
： 引用计数、标记清除、标记整理、分待回收（v8）


引用计数的效率可能想较于其他计算方式比较低，而且无法回收循环引用的对象

标记清除：不标记局部作用域中的内容，但是回收的空间【不连续】，不能空间最大化

标记整理：上一版的增强，添加了一步内存空间的整理，为了回收后空间连续，使空间不再碎片化；不会立即清理空间，清理步骤会暂停工作

V8的高效： 即时编译，内联缓存，内存设限，采用增量标记的算法做垃圾回收


v8的回收机制： 分代回收{新生代存储区、老生代存储区} 不同的代用不同给GC算法

v8常用的GC算法： 分代回收（必用）、空间复制、标记清楚、标记整理、标记增量（用于提高效率）

新生代回收： 赋值算法+标记整理算法
老生代回收： 标记清楚（主要）、标记整理、增量标记


performance 的使用 主要注意内存，有颜色做区分，如果趋势图持续上升代表内存泄漏，如果频率较高的上下波动，说明频繁垃圾收集。

前端工程化的定义和主要解决的问题


前端项目逐渐各端覆盖化。


可重复的事情： 压缩文件、手动上传文件等 ----机器取代人，避免很多人为的错误

多人协同的时候  ，很难



