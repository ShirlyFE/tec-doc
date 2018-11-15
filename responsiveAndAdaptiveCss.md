# Modernizr
Modernizr is a JavaScript library that detects HTML5 and CSS3 features in the user’s browser.
Modernizr checks browser support for HTML5, CSS3, as well as a few other miscellaneous elements and allows for easily writing fallbacks if they're not supported.

# normalize
normalize.css that will help fix inconsistencies in the way browsers render elements.

# Foundation
## DES:
A Framework for any device, medium, and accessibility. Foundation is a family of responsive front-end frameworks that make it easy to design beautiful responsive websites, apps and emails that look amazing on any device. Foundation is semantic, readable, flexible, and completely customizable. We’re constantly adding new resources and code snippets, including these handy HTML templates to help get you started!

Foundation is a highly customizable and powerful front-end framework, designed with ease of use, speed of development and flexible design in mind.

Main features include responsive layout, flexible grid, source ordering, typography, forms, buttons, navigation, media queries, jQuery & JS libraries.

## Major Features
### Semantic:
You can have the cleanest markup without sacrificing the utility and speed of Foundation.
### Mobile First
You can build for small devices first. Then, as devices get larger and larger, layer in more complexity for a complete responsive design.
### Customizable
You can customize your build to include or remove certain elements, as well as define the size of columns, colors, font size and more.
### Professional
Millions of designers and developers depend on Foundation. We have business support, training and consulting to help grow your product or service.

## PROS
Uses REMs instead of pixels
Foundation comes with Interchange, it makes use of media queries to load images responsively and create content that's suited to different browsers and devices.

focusing on mobile design first, Foundation makes designers think on what kind of content is important, relevant and interesting to the users without thinking too much on the space.

## CONS
Documentation is not that good

# Pure Css
## PROS
Easy to customize: Has an extremely minimalist look that is super-easy to customize since it basically gives designers just a foundation on which they can easily build their design.
Lightweight: Extremely small file size: 4.5KB minified + gzip.
Cross-Browser compatibility: A solid base built on Normalize.css to fix cross-browser compatibility issues.
Works well with Bootstrap: Easily use Bootstrap CSS and JS elements within the framework.
## CONS
Not suitable for beginners:
Since Pure CSS only carries a minimum number of styles out of the box, it might not be great for beginners who want a complete style that looks good out of the box without having to customize it.


# Semantic.gs
supports all modern CSS pre-processors sucha as Sass, LESS and Stylus.

By default Semantic Grid System has a fixed layout. But switching to fluid, percentage-based layouts is easy. To switch from pixels to percentages, simply add one variable:
@total-width: 100%;


# Materialize

## PROS
Materialize follows Google's guidelines for Material design, which in theory is device agnostic, Materialize itself is device agnostic too. It's designed to look good on every device.

## CONS
Refuses to use the flexbox model
Large/Heavy

响应式与自适应的原理是相似的，都是检测设备，根据不同的设备采用不同的css，而且css都是采用的百分比的，而不是固定的宽度，不同点是响应式的模板在不同的设备上看上去是不一样的，会随着设备的改变而改变展示样式，而自适应不会，所有的设备看起来都是一套的模板，不过是长度或者图片变小了，不会根据设备采用不同的展示样式，流式就是采用了一些设置，当宽度大于多少时怎么展示，小于多少时怎么展示，而且展示的方式向水流一样，一部分一部分的加载，静态的就是采用固定宽度的了。


flex: flex-grow, flex-shrink, flex-basis 还是先来回顾下三个基本的属性

flex-grow: 定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大

flex-shirnk: 定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小

flex-basis: 定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小

详解
直接通过一个例子进行讲解了，看下面的代码

<style>
.wrap {
	width: 600px;
   display: flex;
}
.item {
	height: 50px;
	line-height: 50px;
	text-align: center;
	color: #fff;
}
.item1 {
	width: 200px;
	flex: 2 1 0%;
	background: #f00;
}
.item2 {
	width: 150px;
	flex: 2 1 auto;
	background: #0f0;
}
.item3 {
	flex: 1 1 200px;
	background: #00f;
}
</style>

<div class="wrap">
	<div class="item item1">item1</div>
	<div class="item item2">item2</div>
	<div class="item item3">item3</div>
</div>
1

具体宽度数值请戳我查看

通过例子发现，实际宽度都不是我们上述设置的宽度或者基准宽度，我们来看看它的计算方式。

主轴总长度为600，子项基准宽度加起来为 0 + 150 + 200 = 350（PS.当flex-basis设置为0%的时候，其设置的宽度将不起任何作用，如item1中的wdith）

因此还剩余宽度为 600 - 350 = 250

flex-grow系数为 2 + 2 + 1 = 5

每一项将分配的剩余宽度计算方式为
w = (flex-grow /所有flex-grow的总和) * 剩余值

因此实际长度为 item1 = 0 + 250*2/5 = 100 或 content的尺寸

item2 = 150 + 250*2/5 = 250

item3 = 200 + 250*1/5 = 250

极端情况，若将wrap宽度改为400，虽然还是会有剩余宽度 400 - 350 = 50，但通过计算后

item1 = 0 + 50*2/5 = 20，已经小余其content的尺寸了，因此item1就等于其content尺寸38.77，此时其他项的剩余宽度将减少 38.77 - 20 = 18.77，各子项尺寸将按flex-grow的比例去分掉这18.77

item2 = 150 + 502/5 - 18.77/32 = 157.48

item3 = 200 + 502/5 - 18.77/31 = 203.74

至于为什么是felx-grow大一些，最后要减掉的多一些，我就不得而知了

当子项基准宽度加起来大于主轴总长度时，子项将会被压缩，请自行修改上述例子wrap的宽度为300查看效果

因为item1基准值为0，其尺寸将直接等于其内容content的尺寸，通过例子可以看到item宽度为38.77px

因此子元素基准值加起来溢出值为 350-(300-38.77) = 88.77

加权值计算公式为 所有子项flex-basis*flex-shrink的总和

s = 01 + 1501 + 200*1 = 350

每项压缩的长度 w = (flex-basis*flex-shrink / s) * 溢出的值

w-item1 = 01/35088.77 = 0;

w-item2 = 1501/35088.77 = 38.044

w-item3 = 2001/35088.77 = 28.57

最终

item1 = content - 0 = content

item2 = 150 - 38.044 = 111.95

item3 = 200 - 28.57 = 149.27

上述例子展示了在计算时的两种情况，可以发现flex-basis基准值起了很重要的作用。

总结:

当值默认为auto时，以此元素设置的尺寸为准，若也是auto，则以内容content尺寸为准
当flex-basis设置了值时，以此设置尺寸的为准
若为百分比，则根据父容器进行计算。若为0%，则其设置的width将无效。
建议在子项中通过设置flex-basis的方式去设置想要的尺寸，而不是设置width
缩写flex:auto(flex: 1 1 auto)，flex:none(0 0 auto)，flex:1(1 1 0%)
若想某项不参与弹性布局的计算，应将其设置成flex:none