# good resources
Learning the details of a company’s technology stack, style guide and engineering philosophy are all important

## Sass, Less, Stylus
Sass是由Ruby开发人员设计和编写,因此，Sass样式表使用的是不带括号、不含分号和严格缩进的Ruby类语法，直到3.0的版本在2010年5月问世，为 Sassy CSS 引入了一种名为SCSS的全新语法.自SCSS发布以来，使SCSS(css-like sytax更容易阅读，更短的学习曲线)与CSS完全兼容，一直是Sass的维护者的首要任务

LESS是受Sass启发而开发的工具，它列出了如下开发的理由：
“为什么要开发一个Sass的替代品呢？原因很简单：首先是语法。Sass的一个关键特性是缩进式的语法，这种语法可以产生柱式外观的代码。但是你需要花费时间学习一门新的语法以及重新构建你现在的样式表。LESS给CSS带来了很多特性，使得LESS能够和CSS无缝地紧密结合在一起。因此，你可以平滑地由CSS迁移到LESS，如果你只是对使用变量或者操作感兴趣的话，你不需要学习一整门全新的语言。”

### 三种预处理器历史
三种CSS 预处理器（框架）历史：SASS2007年诞生，最早也是最成熟的CSS预处理器，拥有ruby社区的支持和compass这一最强大的css框架，目前受LESS影响，已经进化到了全面兼容CSS的SCSS。LESS2009年出现，受SASS的影响较大，但又使用CSS的语法，让大部分开发者和设计师更容易上手，在ruby社区之外支持者远超过SASS，其缺点是比起SASS来，可编程功能不够，不过优点是简单和兼容CSS，反过来也影响了SASS演变到了SCSS的时代，著名的Twitter Bootstrap就是采用LESS做底层语言的。Stylus，2010年产生，来自Node.js社区，主要用来给Node项目进行CSS预处理支持，在此社区之内有一定支持者，在广泛的意义上人气还完全不如SASS和LESS。


## JS PageXOffset
window.pageYOffset, window.pageXOffset: 获得文档在垂直方向或者水平方向的滚动距离
window.screenX, window.screenY: 用户的浏览器左/上边框和用户的屏幕左边之间的距离

clientWidth, clientHeight: 获取元素的内宽度包括内边距(padding)离但不包括垂直滚动条,边框或者外边距(margin)。这里的元素必须是非内联的元素
clientLeft, clientTop: 一个元素左边边框的宽度包含垂直滚动条的宽度但并不包含左外边距和左内边距
event.clientX, event.clientY: 触摸事件中的触摸点相对于视图的左位移不包括滚动的位移(这两个可以应用在下拉刷新的插件(当是touch事件的时候), 拖拽的插件(当是鼠标事件的时候）)

window.innerWidth指的是窗口视窗的宽度包括垂直滚动条的宽度
window.innerHeight窗口视窗的高度包括水平滚动条的高度
window.outerHeight指的是包括整个浏览器的容器的高度
window.outerHeight类似


offsetWidth元素的宽度包括元素的边框，元素水平内边距和垂直滚动条的宽度。offsetHeight与之类似，这里获得的数值是四舍五入到整数，如果想要获取精益的数值可以用element.getBoundingClientRect()来获取
offsetLeft是获取元素相对于最近的定位的元素的左边距离,offsetTop类似
内联元素是用的Element.getClientRects来获得宽度和高度
getBoundingClientRect获取top与offsetTop的区别:
    这里的getBoundingClientRect是以视图上的位置为基准的即相对于viewport的上，右，下，左的距离，而offsetTop是以最近的定位的元素，这里的定位元素,如果目标元素是fixed或者目标元素的最近的父元素是static定位的话的话两者是相等的,否则一般是不相等(排队了元素未在视图顶部的情况)。一个是相对于视图，一个是相对于最近的有定位的父元素。

scrollTop，scrollLeft: 获取或者设置一个元素垂直方向滚动的距离，表示的意思是元素方框的顶部和元素的最顶部的可见内容之间的距离。当一个元素内容没有产生垂直滚动条，那么这个值为0。
scrollWidth, scrollHeight: 











[frontend style guide](http://udacity.github.io/frontend-nanodegree-styleguide/index.html)
[网站渐进式增强体验(PWA)改造：Service Worker 应用详解](https://lzw.me/a/pwa-service-worker.html)

[webAssembly让你的Javascript计算性能提升70%](https://www.cnblogs.com/dojo-lzz/p/8053250.html)

[What is Waiting (TTFB(Time To First Byte)) in DevTools, and what to do about it](https://scaleyourcode.com/blog/article/27)

[Understanding Resource Timing](https://developers.google.com/web/tools/chrome-devtools/network-performance/understanding-resource-timing)

[Scroll to the future](https://evilmartians.com/chronicles/scroll-to-the-future-modern-javascript-css-scrolling-implementations)

[Chrome扩展程序开发基础介绍](http://blog.iderzheng.com/introduction-to-chrome-extension-development/)

[Chrome扩展程序各结构间交互方式](http://blog.iderzheng.com/interaction-between-chrome-extension-components/)

[react.js 之 create-react-app 命令行工具系统讲解](https://www.cnblogs.com/ye-hcj/p/7191153.html)


#### | React 相关

* [深入理解 React 状态管理](https://github.com/happylindz/react-state-management-tutorial)
* [Redux 异步流最佳实践](https://github.com/happylindz/blog/issues/2)
* [剖析单页面应用路由实现原理](https://github.com/happylindz/blog/issues/4)

#### | 前端工程化

* [前端代码异常监控实战](https://github.com/happylindz/blog/issues/5)
* [深入理解 webpack 文件打包机制](https://github.com/happylindz/blog/issues/6)
* [webpack 持久化缓存实践](https://github.com/happylindz/blog/issues/7)
* [webpack 大型应用优化实践](https://github.com/happylindz/blog/issues/8)

#### | ES6 

* [你可能不知道的 Promise 对象](https://github.com/happylindz/blog/issues/1)
* [深入理解 ES6 模块机制](https://github.com/happylindz/blog/issues/10)

#### | 前端基础知识

* [跨域，你需要知道的全在这里](https://github.com/happylindz/blog/issues/3)
* [常见前端面试题总结 (一)](https://github.com/happylindz/blog/issues/9)
* [让 BAT Offer 不再难拿](https://github.com/happylindz/blog/issues/13)

#### | CSS 基础

* [深入理解 CSS 属性 z-index](https://github.com/happylindz/blog/issues/11)
* [纯 CSS 实现多行文字截断](https://github.com/happylindz/blog/issues/12)


### 
[9 Steps: Choosing a tech stack for your web application](https://medium.com/unicorn-supplies/9-steps-how-to-choose-a-technology-stack-for-your-web-application-a6e302398e55)