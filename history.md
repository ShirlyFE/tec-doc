## Start from URL 
Going back to first principles, what does a URL do? It identifies a unique resource. You can link to it directly; you can bookmark it; search engines can index it; you can copy and paste it and email it to someone else, who can click it and end up seeing the same resource you saw originally. These are all excellent qualities. URLs matter

## HTML5 history
The HTML5 history API lets you do this. Instead of triggering a full page refresh, you can use script to, in essence, download half a page. This illusion is tricky to pull off, and it requires some work on your part. Are you watching closely?

Let’s say you have two pages, page A and page B. The two pages are 90% identical; only 10% of the page content is different. The user navigates to page A, then tries to navigate to page B. But instead of triggering a full page refresh, you interrupt this navigation and do the following steps manually:

Load the 10% of the page from page B that is different from page A (probably using XMLHttpRequest). This will require some server-side changes to your web application. You will need to write code to return just the 10% of page B that is different from page A. This can be a hidden URL or query parameter that the end user would not normally see.
Swap in the changed content (using innerHTML or other DOM methods). You may also need to reset any event handlers on elements within the swapped-in content.
Update the browser location bar with the URL of page B, using a particular method from the HTML5 history API that I’ll show you in a moment.

## history
history.length //如果当前窗口先后访问了三个网址，那么history对象就包括三项，history.length属性等于3
在浏览历史中添加和修改记录:
    history.pushState(state, title, url)
        state：一个与指定网址相关的状态对象，popstate事件触发时，该对象会传入回调函数。如果不需要这个对象，此处可以填null。
        title：新页面的标题，但是所有浏览器目前都忽略这个值，因此这里可以填null。
        url：新的网址，必须与当前页面处在同一个域。浏览器的地址栏将显示这个网址。
        // 总之，pushState方法不会触发页面刷新，只是导致history对象发生变化，地址栏会有反应
        // 如果pushState的url参数，设置了一个新的锚点值（即hash），并不会触发hashchange事件。如果设置了一个跨域网址，则会报错。
        ```
        // 报错
        history.pushState(null, null, 'https://twitter.com/hello');
        ```
    history.replaceState()
        history.replaceState方法的参数与pushState方法一模一样，区别是它修改浏览历史中当前纪录。
    // if (!!(window.history && history.pushState)) then support history API
    history.state属性
        返回当前页面的state对象
        ```
            history.pushState({page: 1}, 'title 1', '?page=1');
            history.state
            // { page: 1 }
        ```
popstate事件
    每当同一个文档的浏览历史（即history对象）出现变化时，就会触发popstate事件。

    需要注意的是，仅仅调用pushState方法或replaceState方法 ，并不会触发该事件，只有用户点击浏览器倒退按钮和前进按钮，或者使用JavaScript调用back、forward、go方法时才会触发
    ```
        window.onpopstate = function (event) {
            console.log('location: ' + document.location);
            console.log('state: ' + JSON.stringify(event.state));
        };

        // 或者
        window.addEventListener('popstate', function(event) {
            console.log('location: ' + document.location);
            console.log('state: ' + JSON.stringify(event.state));
        });
    ```
    //注意，页面第一次加载的时候，在load事件发生后，Chrome和Safari浏览器（Webkit核心）会触发popstate事件，而Firefox和IE浏览器不会

移动func:
    history.back() // 返回上一页时，页面通常是从浏览器缓存之中加载，而不是重新要求服务器发送新的网页
    history.forward()
    history.go() //接受一个整数作为参数，移动到该整数指定的页面，比如go(1)相当于forward()，go(-1)相当于back()
    // 如果移动的位置超出了访问历史的边界，以上三个方法并不报错，而是默默的失败。
    // history.go(0)相当于刷新当前页面

URLSearchParams API
    URLSearchParams API用于处理URL之中的查询字符串
    URLSearchParams有以下方法，用来操作某个参数。
        has()：返回一个布尔值，表示是否具有某个参数
        get()：返回指定参数的第一个值
        getAll()：返回一个数组，成员是指定参数的所有值
        set()：设置指定参数
        delete()：删除指定参数
        append()：在查询字符串之中，追加一个键值对
        keys()：遍历所有参数名
        values()：遍历所有参数值
        entries()：遍历所有参数的键值对
        toString()：返回整个查询字符串
        ```
            var paramsString = 'q=URLUtils.searchParams&topic=api';
            var searchParams = new URLSearchParams(paramsString);

            searchParams.has('topic') // true
            searchParams.get('topic') // "api"
            searchParams.getAll('topic') // ["api"]

            searchParams.get('foo') // null，注意Firefox返回空字符串
            searchParams.set('foo', 2);
            searchParams.get('foo') // 2

            searchParams.append('topic', 'webdev');
            searchParams.toString() // "q=URLUtils.searchParams&topic=api&foo=2&topic=webdev"

            searchParams.append('foo', 3);
            searchParams.getAll('foo') // [2, 3]

            searchParams.delete('topic');
            searchParams.toString() // "q=URLUtils.searchParams&foo=2&foo=3"
            for (var key of searchParams.keys()) {
                console.log(key);
            }
            // key1
            // key2

            for (var value of searchParams.values()) {
                console.log(value);
            }
            // value1
            // value2

            for (var pair of searchParams.entries()) {
                console.log(pair[0]+ ', '+ pair[1]);
            }
            // key1, value1
            // key2, value2
        ```
        下面是一个替换当前URL的例子。
        ```
            // URL: https://example.com?version=1.0
            var params = new URLSearchParams(location.search.slice(1));
            params.set('version', 2.0);

            window.history.replaceState({}, '', `${location.pathname}?${params}`);
            // URL: https://example.com?version=2.0
        ```
        URLSearchParams实例可以当作POST数据发送，所有数据都会URL编码。
        ```
            let params = new URLSearchParams();
            params.append('api_key', '1234567890');

            fetch('https://example.com/api', {
                method: 'POST',
                body: params
            }).then(...)
        ```
        DOM的a元素节点的searchParams属性，就是一个URLSearchParams实例。
        ```
            var a = document.createElement('a');
            a.href = 'https://example.com?filter=api';
            a.searchParams.get('filter') // "api"
        ```
        URLSearchParams还可以与URL接口结合使用。
        ```
            var url = new URL(location);
            var foo = url.searchParams.get('foo') || 'somedefault';
        ```


# '#'的涵义
#代表网页中的一个位置。其右面的字符，就是该位置的标识符。比如，
　　http://www.example.com/index.html#print
就代表网页index.html的print位置。浏览器读取这个URL后，会自动将print位置滚动至可视区域。
为网页位置指定标识符，有两个方法。一是使用锚点，比如<a name="print"></a>，二是使用id属性，比如<div id="print" >。

#是用来指导浏览器动作的，对服务器端完全无用。所以，HTTP请求中不包括#。
比如，访问下面的网址，
　　http://www.example.com/index.html#print
浏览器实际发出的请求是这样的：
　　GET /index.html HTTP/1.1
　　Host: www.example.com

在第一个#后面出现的任何字符，都会被浏览器解读为位置标识符。这意味着，这些字符都不会被发送到服务器端。
比如，下面URL的原意是指定一个颜色值：
　　http://www.example.com/?color=#fff
但是，浏览器实际发出的请求是：
　　GET /?color= HTTP/1.1
　　Host: www.example.com
可以看到，"#fff"被省略了。只有将#转码为%23，浏览器才会将其作为实义字符处理。也就是说，上面的网址应该被写成：
　　http://example.com/?color=%23fff

改变#不触发网页重载 --- 单单改变#后的部分，浏览器只会滚动到相应位置，不会重新加载网页
### 改变#会改变浏览器的访问历史
每一次改变#后的部分，都会在浏览器的访问历史中增加一个记录，使用"后退"按钮，就可以回到上一个位置。
这对于ajax应用程序特别有用，可以用不同的#值，表示不同的访问状态，然后向用户给出可以访问某个状态的链接。
值得注意的是，上述规则对IE 6和IE 7不成立，它们不会因为#的改变而增加历史记录。
### window.location.hash读取#值
window.location.hash这个属性可读可写。读取时，可以用来判断网页状态是否改变；写入时，则会在不重载网页的前提下，创造一条访问历史记录
### onhashchange事件
这是一个HTML 5新增的事件，当#值发生变化时，就会触发这个事件
它的使用方法有三种：
　　window.onhashchange = func;
　　<body onhashchange="func();">
　　window.addEventListener("hashchange", func, false);

### Google抓取#的机制
默认情况下，Google的网络蜘蛛忽视URL的#部分。
但是，Google还规定，如果你希望Ajax生成的内容被浏览引擎读取，那么URL中可以使用"#!"，Google会自动将其后面的内容转成查询字符串_escaped_fragment_的值。
比如，Google发现新版twitter的URL如下：
　　http://twitter.com/#!/username
就会自动抓取另一个URL：
　　http://twitter.com/?_escaped_fragment_=/username
通过这种机制，Google就可以索引动态的Ajax内容。


