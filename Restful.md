1. Resource:
REST，即Representational State Transfer的缩写
REST的名称"表现层状态转化"中，省略了主语。"表现层"其实指的是"资源"（Resources）的"表现层"。

每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。

URI只代表资源的实体，不代表它的形式。严格地说，有些网址最后的".html"后缀名是不必要的，因为这个后缀名表示格式，属于"表现层"范畴，而URI应该只代表"资源"的位置。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。

让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。
客户端用到的手段，只能是HTTP协议。具体来说，就是HTTP协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。它们分别对应四种基本操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源。

** 误区
最常见的一种设计错误，就是URI包含动词。因为"资源"表示一种实体，所以应该是名词，URI不应该有动词，动词应该放在HTTP协议中。

举例来说，某个URI是/posts/show/1，其中show是动词，这个URI就设计错了，正确的写法应该是/posts/1，然后用GET方法表示show。
如果某些动作是HTTP动词表示不了的，你就应该把动作做成一种资源。比如网上汇款，从账户1向账户2汇款500元，错误的URI是：
　　POST /accounts/1/transfer/500/to/2
正确的写法是把动词transfer改成名词transaction，资源不能是动词，但是可以是一种服务：
　　POST /transaction HTTP/1.1
　　Host: 127.0.0.1
　　
　　from=1&to=2&amount=500.00
另一个设计误区，就是在URI中加入版本号：
　　http://www.example.com/app/1.0/foo
　　http://www.example.com/app/1.1/foo
　　http://www.example.com/app/2.0/foo
因为不同的版本，可以理解成同一种资源的不同表现形式，所以应该采用同一个URI。版本号可以在HTTP请求头信息的Accept字段中进行区分（参见Versioning REST Services）：
　　Accept: vnd.example-com.foo+json; version=1.0
　　Accept: vnd.example-com.foo+json; version=1.1
　　Accept: vnd.example-com.foo+json; version=2.0

Restful Source: http://www.ruanyifeng.com/blog/2011/09/restful.html
