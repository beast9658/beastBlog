---
title: 面试题笔记——CSS篇
date: 2021-06-18 17:11:19
---
1. CSS选择器及其优先级  


| 选择器     | 格式            | 优先级权重 |
| ------- | ------------- | ----- |
| id选择器   | #id          | 100   |
| 类选择器    | .classname    | 10    |
| 属性选择器   | a[ref=“eee”] | 10    |
| 伪类选择器   | li:last-child | 10    |
| 标签选择器   | div           | 1     |
| 伪元素选择器  | li:after      | 1     |
| 相邻兄弟选择器 | h1+p          | 0     |
| 子选择器    | ul>li         | 0     |
| 后代选择器   | li a          | 0     |
| 通配符选择器  | *            | 0     |

对于选择器的优先级：  
●标签选择器、伪元素选择器：1；  
●类选择器、伪类选择器、属性选择器：10；  
●id 选择器：100；  
●内联样式：1000；  
  
注意事项：  
●!important声明的样式的优先级最高；  
●如果优先级相同，则最后出现的样式生效；  
●继承得到的样式的优先级最低；  
●通用选择器（*）、子选择器（>）和相邻同胞选择器（+）并不在这四个等级中，所以它们的权值都为 0 ；  
●样式表的来源不同时，优先级顺序为：内联样式 > 内部样式 > 外部样式 > 浏览器用户自定义样式 > 浏览器默认样式。  
2. CSS中可继承与不可继承属性有哪些  
一、无继承性的属性  
1display：规定元素应该生成的框的类型  
2文本属性：  
●vertical-align：垂直文本对齐  
●text-decoration：规定添加到文本的装饰  
●text-shadow：文本阴影效果  
●white-space：空白符的处理  
●unicode-bidi：设置文本的方向  
3盒子模型的属性：width、height、margin、border、padding  
4背景属性：background、background-color、background-image、background-repeat、background-position、background-attachment  
5定位属性：float、clear、position、top、right、bottom、left、min-width、min-height、max-width、max-height、overflow、clip、z-index  
6生成内容属性：content、counter-reset、counter-increment  
7轮廓样式属性：outline-style、outline-width、outline-color、outline  
8页面样式属性：size、page-break-before、page-break-after  
9声音样式属性：pause-before、pause-after、pause、cue-before、cue-after、cue、play-during  
  
二、有继承性的属性  
1字体系列属性  
●font-family：字体系列  
●font-weight：字体的粗细  
●font-size：字体的大小  
●font-style：字体的风格  
2文本系列属性  
●text-indent：文本缩进  
●text-align：文本水平对齐  
●line-height：行高  
●word-spacing：单词之间的间距  
●letter-spacing：中文或者字母之间的间距  
●text-transform：控制文本大小写（就是uppercase、lowercase、capitalize这三个）  
●color：文本颜色  
3元素可见性  
●visibility：控制元素显示隐藏  
4列表布局属性  
●list-style：列表风格，包括list-style-type、list-style-image等  
5光标属性  
●cursor：光标显示为何种形态  
3. display的属性值及其作用  


| 属性值          | 作用                            |
| ------------ | ----------------------------- |
| none         | 元素不显示，并且会从文档流中移除。             |
| block        | 块类型。默认宽度为父元素宽度，可设置宽高，换行显示。    |
| inline       | 行内元素类型。默认宽度为内容宽度，不可设置宽高，同行显示。 |
| inline-block | 默认宽度为内容宽度，可以设置宽高，同行显示。        |
| list-item    | 像块类型元素一样显示，并添加样式列表标记。         |
| table        | 此元素会作为块级表格来显示。                |
| inherit      | 规定应该从父元素继承display属性的值。        |

4. display的block、inline和inline-block的区别  
　（1）block：会独占一行，多个元素会另起一行，可以设置width、height、margin和padding属性；  
　（2）inline：元素不会独占一行，设置width、height属性无效。但可以设置水平方向的margin和padding属性，不能设置垂直方向的padding和margin；  
　（3）inline-block：将对象设置为inline对象，但对象的内容作为block对象呈现，之后的内联对象会被排列在同一行内。  
  
对于行内元素和块级元素，其特点如下：  
（1）行内元素  
●设置宽高无效；  
●可以设置水平方向的margin和padding属性，不能设置垂直方向的padding和margin；  
●不会自动换行；  
（2）块级元素  
●可以设置宽高；  
●设置margin和padding都有效；  
●可以自动换行；  
●多个块状，默认排列从上到下。  
5. 隐藏元素的方法有哪些  
●display: none：渲染树不会包含该渲染对象，因此该元素不会在页面中占据位置，也不会响应绑定的监听事件。  
●visibility: hidden：元素在页面中仍占据空间，但是不会响应绑定的监听事件。  
●opacity: 0：将元素的透明度设置为 0，以此来实现元素的隐藏。元素在页面中仍然占据空间，并且能够响应元素绑定的监听事件。  
●position: absolute：通过使用绝对定位将元素移除可视区域内，以此来实现元素的隐藏。  
●z-index: 负值：来使其他元素遮盖住该元素，以此来实现隐藏。  
●clip/clip-path ：使用元素裁剪的方法来实现元素的隐藏，这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。  
●transform: scale(0,0)：将元素缩放为 0，来实现元素的隐藏。这种方法下，元素仍在页面中占据位置，但是不会响应绑定的监听事件。  
6. link和@import的区别  
两者都是外部引用CSS的方式，它们的区别如下：  
●link是XHTML标签，除了加载CSS外，还可以定义RSS等其他事务；@import属于CSS范畴，只能加载CSS。  
●link引用CSS时，在页面载入时同时加载；@import需要页面网页完全载入以后加载。  
●link是XHTML标签，无兼容问题；@import是在CSS2.1提出的，低版本的浏览器不支持。  
●link支持使用Javascript控制DOM去改变样式；而@import不支持。  
7. transition和animation的区别  
●transition是过渡属性，强调过度，它的实现需要触发一个事件（比如鼠标移动上去，焦点，点击等）才执行动画。它类似于flash的补间动画，设置一个开始关键帧，一个结束关键帧。  
●animation是动画属性，它的实现不需要触发事件，设定好时间之后可以自己执行，且可以循环一个动画。它也类似于flash的补间动画，但是它可以设置多个关键帧（用@keyframe定义）完成动画。  
8. display:none与visibility:hidden的区别  
这两个属性都是让元素隐藏，不可见。两者区别如下：  
（1）在渲染树中  
●display:none会让元素完全从渲染树中消失，渲染时不会占据任何空间；  
●visibility:hidden不会让元素从渲染树中消失，渲染的元素还会占据相应的空间，只是内容不可见。  
（2）是否是继承属性  
●display:none是非继承属性，子孙节点会随着父节点从渲染树消失，通过修改子孙节点的属性也无法显示；  
●visibility:hidden是继承属性，子孙节点消失是由于继承了hidden，通过设置visibility:visible可以让子孙节点显示；  
（3）修改常规文档流中元素的 display 通常会造成文档的重排，但是修改visibility属性只会造成本元素的重绘；  
（4）如果使用读屏器，设置为display:none的内容不会被读取，设置为visibility:hidden的内容会被读取。  
9. 伪元素和伪类的区别和作用？  
●伪元素：在内容元素的前后插入额外的元素或样式，但是这些元素实际上并不在文档中生成。它们只在外部显示可见，但不会在文档的源代码中找到它们，因此，称为“伪”元素。例如：  
●伪类：将特殊的效果添加到特定选择器上。它是已有元素上添加类别的，不会产生新的元素。例如：  
总结：伪类是通过在元素选择器上加⼊伪类改变元素状态，⽽伪元素通过对元素的操作进⾏对元素的改变。  
10. 对requestAnimationframe的理解  
实现动画效果的方法比较多，Javascript 中可以通过定时器 setTimeout 来实现，CSS3 中可以使用 transition 和 animation 来实现，HTML5 中的 canvas 也可以实现。除此之外，HTML5 提供一个专门用于请求动画的API，那就是 requestAnimationFrame，顾名思义就是请求动画帧。  
  
MDN对该方法的描述：  
window.requestAnimationFrame() 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。  
  
语法： window.requestAnimationFrame(callback); 其中，callback是下一次重绘之前更新动画帧所调用的函数(即上面所说的回调函数)。该回调函数会被传入DOMHighResTimeStamp参数，它表示requestAnimationFrame() 开始去执行回调函数的时刻。该方法属于宏任务，所以会在执行完微任务之后再去执行。  
  
取消动画：使用cancelAnimationFrame()来取消执行动画，该方法接收一个参数——requestAnimationFrame默认返回的id，只需要传入这个id就可以取消动画了。  
  
优势：  
●CPU节能：使用SetTinterval 实现的动画，当页面被隐藏或最小化时，SetTinterval 仍然在后台执行动画任务，由于此时页面处于不可见或不可用状态，刷新动画是没有意义的，完全是浪费CPU资源。而RequestAnimationFrame则完全不同，当页面处理未激活的状态下，该页面的屏幕刷新任务也会被系统暂停，因此跟着系统走的RequestAnimationFrame也会停止渲染，当页面被激活时，动画就从上次停留的地方继续执行，有效节省了CPU开销。  
●函数节流：在高频率事件( resize, scroll 等)中，为了防止在一个刷新间隔内发生多次函数执行，RequestAnimationFrame可保证每个刷新间隔内，函数只被执行一次，这样既能保证流畅性，也能更好的节省函数执行的开销，一个刷新间隔内函数执行多次时没有意义的，因为多数显示器每16.7ms刷新一次，多次绘制并不会在屏幕上体现出来。  
●减少DOM操作：requestAnimationFrame 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒60帧。  
  
setTimeout执行动画的缺点：它通过设定间隔时间来不断改变图像位置，达到动画效果。但是容易出现卡顿、抖动的现象；原因是：  
●settimeout任务被放入异步队列，只有当主线程任务执行完后才会执行队列中的任务，因此实际执行时间总是比设定时间要晚；  
●settimeout的固定时间间隔不一定与屏幕刷新间隔时间相同，会引起丢帧。  
11. 对盒模型的理解  
CSS3中的盒模型有以下两种：标准盒子模型、IE盒子模型  


![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1168ab130511420ba254e04499bdec93~tplv-k3u1fbpfcp-zoom-1.image)

  


![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6d30a4e63a064160bac8ee0c0462017b~tplv-k3u1fbpfcp-zoom-1.image)

  
盒模型都是由四个部分组成的，分别是margin、border、padding和content。  
  
标准盒模型和IE盒模型的区别在于设置width和height时，所对应的范围不同：  
●标准盒模型的width和height属性的范围只包含了content，  
●IE盒模型的width和height属性的范围包含了border、padding和content。  
  
可以通过修改元素的box-sizing属性来改变元素的盒模型：  
●box-sizing: content-box表示标准盒模型（默认值）  
●box-sizing: border-box表示IE盒模型（怪异盒模型）  
12. 为什么有时候⽤translate来改变位置⽽不是定位？  
translate 是 transform 属性的⼀个值。改变transform或opacity不会触发浏览器重新布局（reflow）或重绘（repaint），只会触发复合（compositions）。⽽改变绝对定位会触发重新布局，进⽽触发重绘和复合。transform使浏览器为元素创建⼀个 GPU 图层，但改变绝对定位会使⽤到 CPU。 因此translate()更⾼效，可以缩短平滑动画的绘制时间。 ⽽translate改变位置时，元素依然会占据其原始空间，绝对定位就不会发⽣这种情况。  
13. li 与 li 之间有看不见的空白间隔是什么原因引起的？如何解决？  
浏览器会把inline内联元素间的空白字符（空格、换行、Tab等）渲染成一个空格。为了美观，通常是一个 li 放在一行，这导致 li 换行后产生换行字符，它变成一个空格，占用了一个字符的宽度。  
  
解决办法：  
（1）为 li 设置float:left。不足：有些容器是不能设置浮动，如左右切换的焦点图等。  
（2）将所有 li 写在同一行。不足：代码不美观。  
（3）将 ul 内的字符尺寸直接设为0，即font-size:0。不足： ul 中的其他字符尺寸也被设为0，需要额外重新设定其他字符尺寸，且在Safari浏览器依然会出现空白间隔。  
（4）消除 ul 的字符间隔letter-spacing:-8px，不足：这也设置了 li 内的字符间隔，因此需要将 li 内的字符间隔设为默认letter-spacing:normal。  
14. CSS3中有哪些新特性  
●新增各种CSS选择器 （: not(.input)：所有 class 不是“input”的节点）  
●圆角 （border-radius:8px）  
●多列布局 （multi-column layout）  
●阴影和反射 （Shadoweflect）  
●文字特效 （text-shadow）  
●文字渲染 （Text-decoration）  
●线性渐变 （gradient）  
●旋转 （transform）  
●增加了旋转,缩放,定位,倾斜,动画,多背景  
15. 替换元素的概念及计算规则  
通过修改某个属性值呈现的内容就可以被替换的元素就称为“替换元素”。  
  
替换元素除了内容可替换这一特性以外，还有以下特性：  
●内容的外观不受页面上的CSS的影响：用专业的话讲就是在样式表现在CSS作用域之外。如何更改替换元素本身的外观需要类似appearance属性，或者浏览器自身暴露的一些样式接口。  
●有自己的尺寸：在Web中，很多替换元素在没有明确尺寸设定的情况下，其默认的尺寸（不包括边框）是300像素×150像素，如  
●在很多CSS属性上有自己的一套表现规则：比较具有代表性的就是vertical-align属性，对于替换元素和非替换元素，vertical-align属性值的解释是不一样的。比方说vertical-align的默认值的baseline，很简单的属性值，基线之意，被定义为字符x的下边缘，而替换元素的基线却被硬生生定义成了元素的下边缘。  
●所有的替换元素都是内联水平元素：也就是替换元素和替换元素、替换元素和文字都是可以在一行显示的。但是，替换元素默认的display值却是不一样的，有的是inline，有的是inline-block。  
  
替换元素的尺寸从内而外分为三类：  
●固有尺寸： 指的是替换内容原本的尺寸。例如，图片、视频作为一个独立文件存在的时候，都是有着自己的宽度和高度的。  
●HTML尺寸： 只能通过HTML原生属性改变，这些HTML原生属性包括的width和height属性、的size属性。  
●CSS尺寸： 特指可以通过CSS的width和height或者max-width/min-width和max-height/min-height设置的尺寸，对应盒尺寸中的content box。  
  
这三层结构的计算规则具体如下：  
（1）如果没有CSS尺寸和HTML尺寸，则使用固有尺寸作为最终的宽高。  
（2）如果没有CSS尺寸，则使用HTML尺寸作为最终的宽高。  
（3）如果有CSS尺寸，则最终尺寸由CSS属性决定。  
（4）如果“固有尺寸”含有固有的宽高比例，同时仅设置了宽度或仅设置了高度，则元素依然按照固有的宽高比例显示。  
（5）如果上面的条件都不符合，则最终宽度表现为300像素，高度为150像素。  
（6）内联替换元素和块级替换元素使用上面同一套尺寸计算规则。  
16. 常见的图片格式及使用场景  
（1）BMP，是无损的、既支持索引色也支持直接色的点阵图。这种图片格式几乎没有对数据进行压缩，所以BMP格式的图片通常是较大的文件。  
  
（2）GIF是无损的、采用索引色的点阵图。采用LZW压缩算法进行编码。文件小，是GIF格式的优点，同时，GIF格式还具有支持动画以及透明的优点。但是GIF格式仅支持8bit的索引色，所以GIF格式适用于对色彩要求不高同时需要文件体积较小的场景。  
  
（3）JPEG是有损的、采用直接色的点阵图。JPEG的图片的优点是采用了直接色，得益于更丰富的色彩，JPEG非常适合用来存储照片，与GIF相比，JPEG不适合用来存储企业Logo、线框类的图。因为有损压缩会导致图片模糊，而直接色的选用，又会导致图片文件较GIF更大。  
  
（4）PNG-8是无损的、使用索引色的点阵图。PNG是一种比较新的图片格式，PNG-8是非常好的GIF格式替代者，在可能的情况下，应该尽可能的使用PNG-8而不是GIF，因为在相同的图片效果下，PNG-8具有更小的文件体积。除此之外，PNG-8还支持透明度的调节，而GIF并不支持。除非需要动画的支持，否则没有理由使用GIF而不是PNG-8。  
  
（5）PNG-24是无损的、使用直接色的点阵图。PNG-24的优点在于它压缩了图片的数据，使得同样效果的图片，PNG-24格式的文件大小要比BMP小得多。当然，PNG24的图片还是要比JPEG、GIF、PNG-8大得多。  
  
（6）SVG是无损的矢量图。SVG是矢量图意味着SVG图片由直线和曲线以及绘制它们的方法组成。当放大SVG图片时，看到的还是线和曲线，而不会出现像素点。SVG图片在放大时，不会失真，所以它适合用来绘制Logo、Icon等。  
  
（7）WebP是谷歌开发的一种新图片格式，WebP是同时支持有损和无损压缩的、使用直接色的点阵图。从名字就可以看出来它是为Web而生的，什么叫为Web而生呢？就是说相同质量的图片，WebP具有更小的文件体积。现在网站上充满了大量的图片，如果能够降低每一个图片的文件大小，那么将大大减少浏览器和服务器之间的数据传输量，进而降低访问延迟，提升访问体验。目前只有Chrome浏览器和Opera浏览器支持WebP格式，兼容性不太好。  
●在无损压缩的情况下，相同质量的WebP图片，文件大小要比PNG小26%；  
●在有损压缩的情况下，具有相同图片精度的WebP图片，文件大小要比JPEG小25%~34%；  
●WebP图片格式支持图片透明度，一个无损压缩的WebP图片，如果要支持透明度只需要22%的格外文件大小。  
17. 对 CSSSprites 的理解  
CSSSprites（精灵图），将一个页面涉及到的所有图片都包含到一张大图中去，然后利用CSS的 background-image，background-repeat，background-position属性的组合进行背景定位。  
  
优点：  
●利用CSS Sprites能很好地减少网页的http请求，从而大大提高了页面的性能，这是CSS Sprites最大的优点；  
●CSS Sprites能减少图片的字节，把3张图片合并成1张图片的字节总是小于这3张图片的字节总和。  
  
缺点：  
●在图片合并时，要把多张图片有序的、合理的合并成一张图片，还要留好足够的空间，防止板块内出现不必要的背景。在宽屏及高分辨率下的自适应页面，如果背景不够宽，很容易出现背景断裂；  
●CSSSprites在开发的时候相对来说有点麻烦，需要借助photoshop或其他工具来对每个背景单元测量其准确的位置。  
●维护方面：CSS Sprites在维护的时候比较麻烦，页面背景有少许改动时，就要改这张合并的图片，无需改的地方尽量不要动，这样避免改动更多的CSS，如果在原来的地方放不下，又只能（最好）往下加图片，这样图片的字节就增加了，还要改动CSS。  
18. 什么是物理像素，逻辑像素和像素密度，为什么在移动端开发时需要用到@3x, @2x这种图片？  
以 iPhone XS 为例，当写 CSS 代码时，针对于单位 px，其宽度为 414px & 896px，也就是说当赋予一个 DIV元素宽度为 414px，这个 DIV 就会填满手机的宽度；  
  
而如果有一把尺子来实际测量这部手机的物理像素，实际为 1242*2688 物理像素；经过计算可知，1242/414=3，也就是说，在单边上，一个逻辑像素=3个物理像素，就说这个屏幕的像素密度为 3，也就是常说的 3 倍屏。  
  
对于图片来说，为了保证其不失真，1 个图片像素至少要对应一个物理像素，假如原始图片是 500300 像素，那么在 3 倍屏上就要放一个 1500900 像素的图片才能保证 1 个物理像素至少对应一个图片像素，才能不失真。  


![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/40043fe4fbbc432f93047a91e3a27e29~tplv-k3u1fbpfcp-zoom-1.image)

  
当然，也可以针对所有屏幕，都只提供最高清图片。虽然低密度屏幕用不到那么多图片像素，而且会因为下载多余的像素造成带宽浪费和下载延迟，但从结果上说能保证图片在所有屏幕上都不会失真。  
  
还可以使用 CSS 媒体查询来判断不同的像素密度，从而选择不同的图片:  


```js
{

    my-image { background: (low.png); }

    @media only screen and (min-device-pixel-ratio: 1.5) {

    #my-image { background: (high.png); }

}
```

19. margin 和 padding 的使用场景  
●需要在border外侧添加空白，且空白处不需要背景（色）时，使用 margin；  
●需要在border内测添加空白，且空白处需要背景（色）时，使用 padding。  
20. 对line-height 的理解及其赋值方式  
（1）line-height的概念：  
●line-height 指一行文本的高度，包含了字间距，实际上是下一行基线到上一行基线距离；  
●如果一个标签没有定义 height 属性，那么其最终表现的高度由 line-height 决定；  
●一个容器没有设置高度，那么撑开容器高度的是 line-height，而不是容器内的文本内容；  
●把 line-height 值设置为 height 一样大小的值可以实现单行文字的垂直居中；  
●line-height 和 height 都能撑开一个高度；  
（2）line-height 的赋值方式：  
●带单位：px 是固定值，而 em 会参考父元素 font-size 值计算自身的行高  
●纯数字：会把比例传递给后代。例如，父级行高为 1.5，子元素字体为 18px，则子元素行高为 1.5 * 18 = 27px  
●百分比：将计算后的值传递给后代  
