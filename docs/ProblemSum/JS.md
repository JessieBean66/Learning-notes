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















