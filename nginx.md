# nginx
Nginx是一款轻量级，高性能的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器

其特点是占有内存少，并发能力强

1)Nginx 是一个很强大的高性能Web和反向代理服务器，它具有很多非常优越的特性；

2)Nginx 作为负载均衡服务器：Nginx 既可以在内部直接支持 Rails 和 PHP 程序对外进行服务，也可以支持作为 HTTP代理服务器对外进行服务；

3)Nginx 同时也是一个非常优秀的邮件代理服务器;

4)Nginx 采用C进行编写，不论是系统资源开销还是CPU使用效率都比 Perlbal 要好很多。

5)Nginx 是一个安装非常的简单，配置文件非常简洁（还能够支持perl语法），Bugs非常少的服务器：Nginx 启动特别容易，并且能够平滑升级。

## Nginx 特点

+ 1）快
+ 2）高扩展性
+ 3）高可靠性
+ 4）低内存消耗
+ 5）单机支持10w+并发连接
+ 6）热部署
+ 7）自由的BSD许可协议


## nginx反向代理深入理解及session共享
一、反向代理详解
上次使用nginx做负载均衡，只使用了一个参数weight，详细的参数今天来八一八；

upstream newServer{
	server 192.168.0.1:8081 down;
	server 192.168.0.2:80 weight=100;
	server 192.168.0.3:9194;
	server 192.168.0.3:9194 max_fails=120 fail_timeout=12;
	server 192.168.0.4:8544 backup; 
}

在需要负载的server节点下添加
proxy_pass http://newServer

upstream 解释:
down 表示当前server 不参与负载
weight 默认为1 数字越大 负载权重越大
max_fails 允许请求失败的次数 当超过最大次数 返回proxy_next_upstream模块定义的错误
fail_timeout:max_fails次失败后，暂停的时间
backup: 其他所有的非backup机器down或者忙的时候 请求这台机器，这台机器的压力最小


可以同时设置多个upstream 来服务于多个server
但是一个server只能使用一个 upstream


二、反向代理sesioon共享问题
如果使用反向代理，负载均衡，那么就会有个问题，我登录的使用使用的服务器1，而跳转到首页的时候，负载到了

服务器二，而服务器二并没有服务器一的登录信息，又会需要登录，这样可能会出现登录死循环，那么怎么解决

登录信息（session）共享问题呢


方法一.使用redis或者数据库之类的存储session

方法二.负载加入 ip_hash 
      这样每个请求来的ip会请求固定的一台服务器

我们用上一次的例子来测试一下ip_hahs



upstream backend  {  
  server 127.0.0.1:9501 weight=65;  
  server 127.0.0.1:9502 weight=105;  
  server 127.0.0.1:9503 weight=45;  
  server 127.0.0.1:9504 weight=85;  
  ip_hash;
}  
只需要在最后一行加入ip_hash;即可

我们不断刷新浏览器，始终访问到的是 服务器四

在终端中使用 curl 始终访问的是服务器

## 反向代理与正向代理的区别
正向代理的过程，它隐藏了真实的请求客户端，服务端不知道真实的客户端是谁，客户端请求的服务都被代理服务器代替来请求

比如我是一个用户，我访问不了某网站，但是我能访问一个代理服务器，这个代理服务器呢,他能访问那个我不能访问的网站，于是我先连上代理服务器,告诉他我需要那个无法访问网站的内容，代理服务器去取回来,然后返回给我。从网站的角度，只在代理服务器来取内容的时候有一次记录，有时候并不知道是用户的请求，也隐藏了用户的资料，这取决于代理告不告诉网站。

总结来说：正向代理 是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。


反向代理隐藏了真实的服务端，当我们请求 www.baidu.com 的时候，就像拨打10086一样，背后可能有成千上万台服务器为我们服务，但具体是哪一台，你不知道，也不需要知道，你只需要知道反向代理服务器是谁就好了


初次接触方向代理的感觉是，客户端是无感知代理的存在的，反向代理对外都是透明的，访问者者并不知道自己访问的是一个代理。因为客户端不需要任何配置就可以访问。

反向代理（Reverse Proxy）实际运行方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器。

反向代理的作用：

（1）保证内网的安全，可以使用反向代理提供WAF功能，阻止web攻击

大型网站，通常将反向代理作为公网访问地址，Web服务器是内网。

（2）负载均衡，通过反向代理服务器来优化网站的负载

两者的区别在于代理的对象不一样：正向代理代理的对象是客户端，反向代理代理的对象是服务端


nginx通过proxy_pass_http 配置代理站点，upstream实现负载均衡。

## Nginx SSI
[SSI(server side include)](https://github.com/ShirlyFE/the-front-end-engineering/blob/master/js/SSI(server_side_include).md)
SSI被称为“服务器端包含”或“服务器端嵌入”技术。是一种基于服务器端的网页制作技术

[nginx 笔记](https://github.com/ShirlyFE/the-front-end-engineering/blob/master/js/nginx.md)

[nginx反向代理和负载均衡策略实战案例](https://juejin.im/post/5adc425f518825670f7b6fc8)

[nginx的反向代理功能和缓存功能](https://www.cnblogs.com/f-ck-need-u/p/7684732.html)