> 强调内容

适合显示重要的提示信息，语法为 !> 内容。

!> 一段重要的内容，可以和其他 **Markdown** 语法混用。

> 普通提示

普通的提示信息，比如写 TODO 或者参考内容等。

?> _TODO_ 完善示例


> 行内格式

**加黑**    *斜体*

> 引用块

> 引用块段落一。
>
> 引用块段落二。
>> 内嵌引用块段落一。
>
> ### 引用块内的标题

> 超链接

行内式 [本篇链接](https://jessiebean66.github.io/Learning-notes/#/ "我的个人博客") 链接，带 title。

行内式 [GitHub](https://jessiebean66.github.io/Learning-notes/#/) 链接。

引用式 [博客][1] 链接。

引用式 [GitHub][2] 链接，带 title。

[1]: https://jessiebean66.github.io/Learning-notes/#/
[2]: https://jessiebean66.github.io/Learning-notes/#/ "我的 GitHub 主页"

> 水平分割线

使用一个单独行里的三个或以上 *、- 来生产一条水平分割线，它们之间可以有空格
***

-----

- - -

> 表格

| 编号  | 姓名（左） | 年龄（右） | 性别（中） |
| ----- | :--------  | ---------: | :------:   |
| 0     | 张三       | 28         | 男         |
| 1     | 李四       | 29         | 男         |





> Github 任务列表

- [ ] foo
- bar
- [x] baz
- [ ] bam <~ not working
  - [ ] bim
  - [ ] lim

> 图片处理

缩放

![logo](https://docsify.js.org/_media/icon.svg ':size=WIDTHxHEIGHT')
![logo](https://docsify.js.org/_media/icon.svg ':size=50x100')
![logo](https://docsify.js.org/_media/icon.svg ':size=100')

<!-- 支持按百分比缩放 -->

![logo](https://docsify.js.org/_media/icon.svg ':size=10%')

> 设置图片的 Class或ID

![logo](https://docsify.js.org/_media/icon.svg ':class=someCssClass')    
![logo](https://docsify.js.org/_media/icon.svg ':id=someCssId')

> 设置标题的 id 属性

### 你好，世界！ :id=hello-world






> 忽略编译链接

有时候我们会把其他一些相对路径放到链接上，你必须告诉 docsify 你不需要编译这个链接。 例如：

[link](/demo/)

它将被编译为 <a href="/#/demo/">link</a> 并将加载 /demo/README.md. 可能你想跳转到 /demo/index.html。

现在你可以做到这一点

[link](/demo/ ':ignore')
即将会得到 <a href="/demo/">link</a> html 代码。不要担心，你仍然可以为链接设置标题。

[link](/demo/ ':ignore title')

<a href="/demo/" title="title">link</a>


> 设置链接的 target 属性

[link:target=_blank](/demo ':target=_blank')

[link:target=_self](/demo2 ':target=_self')


